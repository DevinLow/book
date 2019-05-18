### Android部分处理，Linux系统一切都是文件，任何处理，基本都是文件读写问题，操作sysfs节点，只是是读写的过程.  
``` java
package com.devin.bluecontrol;

import android.util.Log;

import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class WriteSysfs {
    private static final String TAG= "WriteSysfs";

    private static final String BLUE_CMD_PATH="/sys/class/blueIR/blue_data";
    private static final String AMP_DATA_PATH="/sys/class/sl7729/amp_data";

    public static int blueIR_start(){
        writeblueIrCmd(0);
        return 0;
    }
    public static int blueIR_exit(){
        writeblueIrCmd(4);
        return 0;
    }
    public static int blueIR_play(){
        writeblueIrCmd(1);
        return 0;
    }
    public static int blueIR_pasue(){
        writeblueIrCmd(1);
        return 0;
    }
    public static int blueIR_pre(){
        writeblueIrCmd(2);
        return 0;
    }
    public static int blueIR_next(){
        writeblueIrCmd(3);
        return 0;
    }
    public static int setBlueDacOut(){
        writeAmpcmd(2);
        return 0;
    }
    public static int setSystemDacout(){
        writeAmpcmd(0);
        return 0;
    }
    public static int setDvdDacOut(){
        writeAmpcmd(1);
        return 0;
    }
    private static int writeblueIrCmd(int cmd){
        try{
            BufferedWriter bufWriter = null;
            bufWriter = new BufferedWriter(new FileWriter(BLUE_CMD_PATH));
            bufWriter.write(cmd + "");
            bufWriter.close();
            Log.d(TAG, "writeblueIrCmd cmd: " + cmd);
        } catch (IOException e) {
            Log.e(TAG, "erro= " + Log.getStackTraceString(e));
        }
        return 0;
    }
    private static int writeAmpcmd(int cmd){
        try{
            BufferedWriter bufWriter = null;
            bufWriter = new BufferedWriter(new FileWriter(AMP_DATA_PATH));
            bufWriter.write(cmd + "");
            bufWriter.close();
            Log.d(TAG, "writeAmpcmd cmd: " + cmd);
        } catch (IOException e) {
            Log.e(TAG, "erro= " + Log.getStackTraceString(e));
        }
        return 0;
    }
}
```
### 蓝牙通信gpio 模拟发码，通信由GPIO完成，实现sysfs 接口，方便上层应用控制
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

static DEFINE_SPINLOCK(lock);
static DEFINE_SPINLOCK(carrier);

static int blueIR_used,gpio_ir_ctrl,gpio_ir_send;
static char key_name[20] = "blueIR";

//位1
int send_bit_high(void)
{
	int error = -1;
	error = gpio_direction_output(gpio_ir_send,0);
	if (error < 0) {
		return -1;
	}
    udelay(570);
    error = gpio_direction_output(gpio_ir_send,1);
	if (error < 0) {
		return -1;
	}
    udelay(1690);
	return 0;
}

//位0
int send_bit_low(void)
{
	int error = -1;
	error = gpio_direction_output(gpio_ir_send,0);
	if (error < 0) {
		return -1;
	}
	udelay(570);
	error= gpio_direction_output(gpio_ir_send,1);
	if (error < 0) {
		return -1;
	}
	udelay(570);
	return 0;
}

//引导码
int send_command(int ir_addr,int command)
{
	int error = -1;
	int i;
	int sendbit;
	unsigned long flags;
	
	spin_lock_irqsave(&lock, flags);
	error = gpio_direction_output(gpio_ir_send,0);
	if (error < 0) {
		return -1;
	}
   	mdelay(9);
	//	udelay(200);
    error = gpio_direction_output(gpio_ir_send,1);
	if (error < 0) {
		return -1;
	}
    mdelay(4);//4.4ms
    udelay(500);
	//	spin_unlock_irqrestore(&lock, flags);


	//	spin_lock_irqsave(&lock, flags);
	//addr
	for(i =0 ; i < 16 ; i++){
		sendbit=((ir_addr<<i)&0x8000)>>15;
		if(sendbit){
			error = send_bit_high();
			if(error < 0){
				return -1;
			}
		}else{
			error = send_bit_low();
			if(error < 0){
				return -1;
			}
		}
	}	
	//code
	//发送正确数据
	for(i =0 ; i < 8 ; i++){
		sendbit =(command>>i)&0x01;
		if(sendbit){
			error = send_bit_high();
			if(error < 0){
				return -1;
			}
		}else{
			error = send_bit_low();
			if(error < 0){
				return -1;
			}
		}
	}
//数据取反发送
	command = ~(command);
	for(i =0 ; i < 8 ; i++){
		sendbit =(command>>i)&0x01;
		if(sendbit){
			error = send_bit_high();
			if(error < 0){
				return -1;
			}
		}else{
			error = send_bit_low();
			if(error < 0){
				return -1;
			}
		}
	}
//	spin_unlock_irqrestore(&lock, flags);


	//stop
//	spin_lock_irqsave(&lock, flags);
	error = gpio_direction_output(gpio_ir_send,0);
	if (error < 0) {
		return -1;
	}
   	udelay(570);//0.56ms
   	error = gpio_direction_output(gpio_ir_send,1);
	if (error < 0) {
		return -1;
	}
	spin_unlock_irqrestore(&lock, flags);
	
	return 0;
}

static ssize_t dat_ir_show(struct device *dev,struct device_attribute *attr,char *buf)
{
	return 0;
}
//cat ../data
static ssize_t dat_ir_store(struct device *dev,		struct device_attribute *attr,
								const char *buf,	size_t size)
{
	int err;
	unsigned int data = 0;	
	
	if (kstrtoul(buf, 10, &data))
		return -EINVAL;

	if( data > 10 ){
		return -EINVAL;
	}
	
	printk("%s echo data : %d \n",__func__,data);
	switch(data){
		case 0:// start
			err = gpio_direction_output(gpio_ir_ctrl,1);
			if (err < 0) {
				printk("gpio_ir_ctrl high fail \n");
				return size;
			}
			msleep(1000);
			err = send_command(0x00ff,0x44);
			if(err < 0){
				return -EINVAL;
			}

			break;
			
		case 1://pause play
			err = send_command(0x00ff,0x51);
			if(err < 0){
				return -EINVAL;
			}
			break;
			
		case 2://preview
			err = send_command(0x00ff,0x45);
			if(err < 0){
				return -EINVAL;
			}
			break;
			
		case 3://next
			err = send_command(0x00ff,0x01);
			if(err < 0){
				return -EINVAL;
			}
				
			break;
		case 4://exit
			err = send_command(0x00ff,0x40);
			if(err < 0){
				return -EINVAL;
			}
			
			err = gpio_direction_output(gpio_ir_ctrl,0);
			if (err < 0) {
				return -1;
			}
			break;

		default:
			printk("[vortex] data should between 0~4\n");
			return -EINVAL; 
	}
	return size;
}

static struct class_attribute blueIR_class_attrs[] = {
        __ATTR(blue_data,0777,dat_ir_show, dat_ir_store),
        __ATTR_NULL,
};

static struct class blueIR_class = {
        .name           = "blueIR",
        .owner          = THIS_MODULE,
        .class_attrs    = blueIR_class_attrs,
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

static int __init ir_bluetooth_init(void)
{
	int ret;
	int value;
	ret = sys_script_get_item(key_name, "blue_use", &value, 1);	
	
	if (ret != 0) {
			printk("blueIR:used type err!\n");
	} else {
			blueIR_used = value;
	}

	if (!blueIR_used) {
			printk("%s exit blueIR_used\n",__func__);
			return -EBUSY;
	}

	ret = sys_script_get_item(key_name, "ctr_pin", &value, 3);
	if(ret >= 0){
			gpio_ir_ctrl = ret;
	}
	else{
			printk("dhf get ctr_pin error \n");
	}

	ret = sys_script_get_item(key_name, "send_pin", &value, 3);
	if(ret >= 0){
			gpio_ir_send = ret;
	}
	else{
			printk("dhf get ctr_pin error \n");
	}

	ret = gpio_request(gpio_ir_send,"ir_send");
	if (ret < 0) {
		gpio_free(gpio_ir_send);
		return -1;
	}

	ret = gpio_request(gpio_ir_ctrl,"ir_ctrl");
	if (ret < 0) {
		gpio_free(gpio_ir_ctrl);
		printk("%s 2 error exit\n",__func__);
		return -1;
	}

	ret = gpio_direction_output(gpio_ir_send,1);//send pin high
	if (ret < 0) {
		printk("dfh gpio_ir_send high error \n");
		return -1;
	}
	ret = gpio_direction_output(gpio_ir_ctrl,0);//control pin low
	if (ret < 0) {
		printk("dfh gpio_ir_ctrl low error \n");
		return -1;
	}
	ret = class_register(&blueIR_class);
	if(ret < 0)
		printk("dfh create class error \n");
	else	
		printk("dfh create class success \n");
	
	return 0;
}

static void ir_bluetoot_exit(void)
{

}

module_init(ir_bluetooth_init);
module_exit(ir_bluetoot_exit);

MODULE_AUTHOR("ligq@tview-tech.com");
MODULE_DESCRIPTION("IR-bluetooth driver");
MODULE_LICENSE("GPL");
```
