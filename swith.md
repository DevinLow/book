### Android 部分android/frameworks/base/services/core/java/com/android/server/WiredAccessoryManager.java   
### 添加宏定义   
```java
    private static final int BIT_HEADSET = (1 << 0);
    private static final int BIT_HEADSET_NO_MIC = (1 << 1);
    private static final int BIT_USB_HEADSET_ANLG = (1 << 2);
    private static final int BIT_USB_HEADSET_DGTL = (1 << 3);
    private static final int BIT_HDMI_AUDIO = (1 << 4);
    private static final int BIT_LINEOUT = (1 << 5);
    private static final int BIT_COIN = (1 << 6);  //add mask
    private static final int SUPPORTED_HEADSETS = (BIT_HEADSET|BIT_HEADSET_NO_MIC|
                                                   BIT_USB_HEADSET_ANLG|BIT_USB_HEADSET_DGTL|
                                                   BIT_HDMI_AUDIO|BIT_LINEOUT);

    private static final String NAME_H2W = "h2w";
    private static final String NAME_USB_AUDIO = "usb_audio";
    private static final String NAME_HDMI_AUDIO = "hdmi_audio";
    private static final String NAME_HDMI = "hdmi";
    private static final String NAME_COIN = "coin"; //定义 key name
```
### 在  private List<UEventInfo> makeObservedUEventList() 增加杰里的swith设备监听   
```java
//	     Monitor JELI
//
//             If the kernel has support for the "coin" switch, use that.  It will be
//             signalled only when the cion driver detect gpio irq .
	    uei = new UEventInfo(NAME_COIN, BIT_COIN, 0, 0);
	    if (uei.checkSwitchExists()) {
                retVal.add(uei);
            } else {
                uei = new UEventInfo(NAME_COIN, BIT_COIN, 0, 0);
                if (uei.checkSwitchExists()) {
                    retVal.add(uei);
                } else {
                    Slog.w(TAG, "This kernel does not have JELI connect support");
                }
            }
```
### uevent 发生变化的时候，会产生回调，在此处筛选我们要的信息，单我们筛选到COIN有变化的时候，将这个信息用广播发出去.  
```java 
 @Override
        public void onUEvent(UEventObserver.UEvent event) {
            if (LOG) Slog.v(TAG, "Headset UEVENT: " + event.toString());

            try {
                String devPath = event.get("DEVPATH");
                String name = event.get("SWITCH_NAME");
                int state = Integer.parseInt(event.get("SWITCH_STATE"));
                //add devin
                if(name.equals(NAME_COIN)){
                    intent.setAction("android.intent.action.coin");
                    intent.putExtra("connect",state);
                    mHandler.post(new Runnable() {
                        @Override
                        public void run() {
                            ActivityManagerNative.broadcastStickyIntent(intent, null, UserHandle.USER_ALL);
                        }
                    });
                }
                synchronized (mLock) {
                    updateStateLocked(devPath, name, state);
                }
            } catch (NumberFormatException e) {
                Slog.e(TAG, "Could not parse switch state from event " + event);
            }
        }
```
### 在需要的地方注册监听
```java
    private void registerHeadsetPlugReceiver() {
        headsetPlugReceiver = new HeadsetPlugReceiver();
        IntentFilter filter = new IntentFilter();
        filter.addAction("android.intent.action.coin");
        registerReceiver(headsetPlugReceiver, filter);
    }

```
### kernel部分杰里蓝牙模块开关检查驱动实现
```c
#define DEBUG 
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/interrupt.h>
#include <linux/platform_device.h>
#include <linux/slab.h>
#include <linux/switch.h>
#include <linux/irq.h>
#include <linux/input.h>
#include <linux/timer.h>
#include <linux/delay.h>
#include <linux/input.h>
#include <linux/workqueue.h>
#include <linux/gpio.h>
#include <linux/power/scenelock.h>
#include <linux/err.h>
#include <linux/spinlock_types.h>

#include <linux/of_device.h>
#include <linux/of_gpio.h>
#include <linux/pinctrl/pinconf-sunxi.h>
#include <linux/sys_config.h>
#include <linux/sysfs.h>
#include <linux/io.h>

#define USE_SYS_CONFIG	1
#define KEY_NUM         1

static char key_name[20] = "coin";

struct key_detect_data {
	struct switch_dev sdev;
	int detect_pin;
	int state;
	int data;
	int irq;
	struct work_struct work;

	spinlock_t lock;
};

struct key_detect_data *switch_data = NULL;

static void key_detect_work(struct work_struct *work)
{
	//disable_irq(switch_data->irq);
	printk(KERN_ERR "gpio = %d\n",__gpio_get_value(switch_data->detect_pin));
	switch_data->state =__gpio_get_value(switch_data->detect_pin);// !switch_data->state;
	printk(KERN_ERR "state = %d\n",switch_data->state);
	switch_set_state(&switch_data->sdev, switch_data->state);
	enable_irq(switch_data->irq);
}
#if 0
static ssize_t key_detect_data_show(struct device *dev,
			struct device_attribute *devattr,
			char *buf)
{
	int data = 0;
	mutex_lock(&switch_data->_mutex);

	data = switch_data->data;

	mutex_unlock(&switch_data->_mutex);

	return sprintf(buf, "%d\n", data);
}

static ssize_t key_detect_data_store(struct device *dev,
			struct device_attribute *devattr,
			const char *buf,
			size_t count)
{

	int data = simple_strtoul(buf, NULL, 0);
	
	mutex_lock(&switch_data->_mutex);

	switch_data->data = data;

	mutex_unlock(&switch_data->_mutex);

	return count;
}

static DEVICE_ATTR(data,S_IRUGO|S_IWUGO|S_IWUSR|S_IWGRP, key_detect_data_show, key_detect_data_store);
#endif
static ssize_t switch_gpio_print_state(struct switch_dev *sdev, char *buf)
{
	struct key_detect_data *switch_data =
		container_of(sdev, struct key_detect_data, sdev);

	return sprintf(buf, "%d\n", switch_data->state);
}

static ssize_t print_headset_name(struct switch_dev *sdev, char *buf)
{
	struct key_detect_data *switch_data =
		container_of(sdev, struct key_detect_data, sdev);

	return sprintf(buf, "%s\n", switch_data->sdev.name);
}

static irqreturn_t gpio_irq_handler(int irq, void *dev_id)
{
	spin_lock(&switch_data->lock);
	disable_irq_nosync(switch_data->irq);
	schedule_work(&switch_data->work);
	spin_unlock(&switch_data->lock);
	return IRQ_HANDLED;
}

/* type: 0:invalid, 1: int; 2:str, 3:gpio*/
static int sys_script_get_item(char *main_name, char *sub_name, int value[],
		                               int type)
{
	struct device_node *node;
	int ret = -1;

	node = of_find_node_by_name(NULL,"coin");
	if (!node) {
		printk(KERN_ERR "of_find_compatible_node  fail\n");
		goto error_exit;
	}

	if (1 == type) {
		if (of_property_read_u32(node, sub_name, value)) {
			printk(KERN_ERR "of_property_read_u32_array %s.%s fail\n",main_name, sub_name);
			goto error_exit;
		} else{
			printk(KERN_ERR "get value : %d ",*value);
			ret = 0;
		}
	} else if (2 == type) {
		const char *str;
	        if (of_property_read_string(node, sub_name, &str)) {
		        pr_info("of_property_read_string %s.%s fail\n",main_name, sub_name);
	                goto error_exit;
	        } else {
	                ret = 0;
	                memcpy((void *)value, str, strlen(str) + 1);
	        }
	}else if(3 == type){
	        ret = of_get_named_gpio(node, sub_name, 0);
	        printk(KERN_ERR "ret = %d \n",ret);
	}
	return ret;
 
error_exit:
        return -1;
}


static int key_detect_probe(struct platform_device *pdev)
{
	int ret = 0;
	int value;
	int key_detect_used = 1;

	ret = sys_script_get_item(key_name, "coin_use", &value, 1);
	
	if (ret != 0) {
		pr_err("key:used type err!\n");
	} else {
		key_detect_used = value;
	}

	if (!key_detect_used) {
		return -EBUSY;
	}

	pr_debug(" coin init ....\n");
	switch_data = kzalloc(sizeof(struct key_detect_data), GFP_KERNEL);
	if (!switch_data) {
		pr_debug(KERN_ERR"%s,line:%d\n", __func__, __LINE__);
		return -ENOMEM;
	}

	platform_set_drvdata(pdev, (void *)switch_data);

	switch_data->sdev.state = 0;
	switch_data->state     = 0;
	switch_data->sdev.name  = "coin";
	switch_data->data       = 0;
	switch_data->sdev.print_name  = print_headset_name;
	switch_data->sdev.print_state = switch_gpio_print_state;
	pr_debug("switch_dev_register     \n");
	ret = switch_dev_register(&switch_data->sdev);
	if (ret < 0) {
		ret = -ENOMEM;
		goto err_switch_dev_register;
	}

	ret = sys_script_get_item(key_name, "key", &value, 3);
	if(ret >= 0){
		switch_data->detect_pin = ret;
	}
	else{
		printk("get coin key error \n");
	}

	if(gpio_request(switch_data->detect_pin, NULL) != 0) {
		pr_err("request gpio failed!\n");
		ret = -EBUSY;
		goto err_gpio_request;
	}
	pr_debug("request gpio success!\n");
	
	gpio_direction_input(switch_data->detect_pin);
	
	switch_data->irq = gpio_to_irq(switch_data->detect_pin);
	if (switch_data->irq < 0) {
		pr_err(KERN_ERR"gpio_to_irq failed......\n");
		ret = switch_data->irq;
		goto err_gpio_request;
	}
	pr_debug("gpio_to_irq success......irq = %d\n",switch_data->irq);

	ret = request_irq(switch_data->irq, gpio_irq_handler,IRQF_TRIGGER_RISING | IRQF_TRIGGER_FALLING | IRQF_ONESHOT, "coin", NULL);
	if (ret < 0)
	{
		pr_err("request_irq   failed \n");
		goto err_irq_request;
	}
	pr_debug("request_irq   success \n");
	spin_lock_init(&switch_data->lock);	
	//device_create_file(switch_data->sdev.dev, &dev_attr_data);
	INIT_WORK(&switch_data->work, key_detect_work);
	schedule_work(&switch_data->work);

	return 0;

err_irq_request:
	free_irq(switch_data->irq,NULL);

err_gpio_request:
	gpio_free(switch_data->detect_pin);
	switch_dev_unregister(&switch_data->sdev);

err_switch_dev_register:
	kfree(switch_data);
	return ret;
}

static int switch_suspend(struct platform_device *pdev,pm_message_t state)
{
	return 0;
}

static int switch_resume(struct platform_device *pdev)
{
	return 0;
}

static void switch_shutdown(struct platform_device *devptr)
{
	return;
}

static int __exit key_detect_remove(struct platform_device *pdev)
{
	struct key_detect_data *switch_data = platform_get_drvdata(pdev);

	cancel_work_sync(&switch_data->work);

	//device_remove_file(switch_data->sdev.dev, &dev_attr_data);
	free_irq(switch_data->irq,NULL);
	gpio_free(switch_data->detect_pin);
	switch_dev_unregister(&switch_data->sdev);
	
	kfree(switch_data);
	return 0;
}

static struct platform_driver key_detect_driver = {
	.probe		= key_detect_probe,
	.remove		= __exit_p(key_detect_remove),
	.driver		= {
		.name	= "coin",
		.owner	= THIS_MODULE,
	},
	.suspend	= switch_suspend,
	.resume		= switch_resume,
	.shutdown   = switch_shutdown,
};

static struct platform_device key_detect_device = {
    .name = "coin",
    .dev = {
    	.platform_data = "key-data",
    }
};

static int __init key_detect_init(void)
{
	int ret = 0;
	
	ret = platform_device_register(&key_detect_device);
	if (ret == 0) {
		ret = platform_driver_register(&key_detect_driver);
	}
	printk("key_detect_init -------------------------- \n");
	return ret;
}

static void __exit key_detect_exit(void)
{
	platform_driver_unregister(&key_detect_driver);
	platform_device_unregister(&key_detect_device);

}
module_init(key_detect_init);
module_exit(key_detect_exit);

MODULE_AUTHOR("LiGuoqiang<ligq@tview-tech.com>");
MODULE_DESCRIPTION("Switch driver");
MODULE_LICENSE("GPL");
```

