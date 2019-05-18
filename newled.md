### 操作方式 利用sysfs 节点，echo 0 > led_data
```
static struct class_attribute newled_class_attrs[] = {
        __ATTR(led_data,0777,led_show, led_store),
        __ATTR_NULL,
};
```

### sys_config.fex 配置
```
[double_led]
led_use = 1
ctr_pin = port:PH02<1><default><default><default>
```
### Makefile Kconfig 文件路径lichee/linux-3.10/drivers/char/
```
config NEWLED
	tristate "newled support"
	default y
	help
	  If you say yes here you get support for newled
```
```
obj-$(CONFIG_NEWLED)		+= newworld_led.o
```
### led 驱动实现，利用timer实现闪烁，具体参见timer_function。 一共有三态，0 红灯长亮  1白灯长亮 2 红白互闪
```c
#include <linux/module.h>
#include <linux/init.h>
#include <linux/input.h>
#include <linux/delay.h>
#include <linux/slab.h>
#include <linux/interrupt.h>
#include <linux/keyboard.h>
#include <linux/ioport.h>
#include <asm/irq.h>
#include <asm/io.h>
#include <linux/timer.h> 
#include <linux/wakelock.h> 
#include <linux/pinctrl/consumer.h>
#include <linux/pinctrl/pinconf-sunxi.h>
#include <linux/platform_device.h>
#include <linux/gpio.h>
#include <linux/gpio_keys.h>
#include <linux/irq.h>

#include <linux/of_device.h>
#include <linux/of_gpio.h>
#include <linux/sys_config.h>
#include <linux/sysfs.h>
#include <linux/pwm.h>
#include <linux/types.h>
#include <linux/timer.h>       //timer结构体和相关函数的头文件
#include <linux/jiffies.h>     //jiffies和HZ的头文件

#if 1
#define __inf(msg...)       do{printk(KERN_WARNING "[newled] %s,line:%d:",__func__,__LINE__);printk(msg);}while (0)
#define __msg(msg...)       do{printk(KERN_WARNING "[newled] %s,line:%d:",__func__,__LINE__);printk(msg);}while (0)
#define __wrn(msg...)       do{printk(KERN_WARNING "[newled] %s,line:%d:",__func__,__LINE__);printk(msg);}while (0)
#define __here__            do{printk(KERN_WARNING "[newled] %s,line:%d\n",__func__,__LINE__);}while (0)
#define __debug(msg...)     do{printk(KERN_DEBUG "[newled] %s,line:%d:",__func__,__LINE__);printk(msg);}while (0)
#else
#define __inf(msg...)  printk(msg)
#define __msg(msg...)  printk(msg)
#define __wrn(msg...)  printk(msg)
#define __here__
#define __debug(msg...)
#endif

static int flag=0;
static char * key_name ="double_led";
static int gpio_ctrl;
struct timer_list my_timer; 
static int gpio_state = 0;

static ssize_t led_show(struct device *dev,struct device_attribute *attr,char *buf)
{
	return 0;
}
static ssize_t led_store(struct device * dev,struct device_attribute * attr,const char * buf,size_t  size)
{
	int err;
	unsigned int data = 0;	
	
	if (kstrtoul(buf, 10, &data))
		return -EINVAL;

	if( data > 10 ){
		return -EINVAL;
	}
	flag=data;
	__debug("%s echo data : %d \n",__func__,data);

	return size;
}

static struct class_attribute newled_class_attrs[] = {
        __ATTR(led_data,0777,led_show, led_store),
        __ATTR_NULL,
};

static struct class newled_class = {
        .name           = "newled",
        .owner          = THIS_MODULE,
        .class_attrs    = newled_class_attrs,
};
/* type: 0:invalid, 1: int; 2:str, 3:gpio*/
static int sys_script_get_item(char *main_name, char *sub_name, int value[],
                                               int type)
{
        struct device_node *node;
        int ret = -1;
        node = of_find_node_by_name(NULL,main_name);
        if (!node) {
                printk(KERN_ERR "of_find_compatible_node  fail\n");
                goto error_exit;
        }

        if (1 == type) {
                if (of_property_read_u32(node, sub_name, value)) {
                        printk(KERN_ERR "of_property_read_u32_array %s.%s fail\n",main_name, sub_name);
                        goto error_exit;
                } else{
                        printk(KERN_ERR "dhf get value : %d ",*value);
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
	printk(KERN_ERR "%s exit error\n",__func__);
        return -1;
}

void timer_function(void)
{
//high is red ,low is white
   if(flag==0){
	// red led allowways on 
   	if(gpio_state==0){
		gpio_direction_output(gpio_ctrl,1);
		gpio_state=1;	
	}else{
	
	}
   }else if(flag==1){
	if(gpio_state==0){
			
	}else{
		gpio_direction_output(gpio_ctrl,0);
		gpio_state=0;
	}
   }else if(flag==2){
	if(gpio_state==0){
		gpio_direction_output(gpio_ctrl,1);
		gpio_state=1;	
	}else{
		gpio_direction_output(gpio_ctrl,0);
		gpio_state=0;
	}
   }else{
	__wrn("unknow type\n");
   }
   my_timer.expires=jiffies + 250*(1000/HZ);
   add_timer(&my_timer);
}
static int __init newled_init(void)
{
	int ret;
	int value;
	ret = sys_script_get_item(key_name, "led_use", &value, 1);	
	if (ret != 0) {
		return -EBUSY;
		__wrn("newled:used type err!\n");
	}
	ret = sys_script_get_item(key_name, "ctr_pin", &value, 3);
	if(ret >= 0){
			gpio_ctrl = ret;
	}
	else{
		__wrn("newled get ctr_pin error \n");
	}
	ret = gpio_request(gpio_ctrl,"new_led");
	if (ret < 0) {
		gpio_free(gpio_ctrl);
		return -1;
	}
	ret = class_register(&newled_class);
	if(ret < 0){
		__wrn(" create class error \n");
		goto claserr;
	}
	else	
		__wrn("create class success \n");
	init_timer(&my_timer);
	my_timer.function=&timer_function;
 	my_timer.expires=jiffies + 250*(1000/HZ);
	add_timer(&my_timer);
	return 0;

claserr:
	class_unregister(&newled_class);
	return -EINVAL;
}

static void newled_exit(void)
{
	class_unregister(&newled_class);
	del_timer(&my_timer);
}

module_init(newled_init);
module_exit(newled_exit);

MODULE_AUTHOR("gang_liu1987@163.com");
MODULE_DESCRIPTION("newled control driver");
MODULE_LICENSE("GPL");
```
