# **Android 笔记**
### **编码显示Android firmware version 之类的信息**
```java
import android.os.Build;
import android.os.Build.VERSION_CODES;

TextView.setText(Build.VERSION.SDK);//SDK版本号 
TextView.setText(Build.VERSION.RELEASE);//Firmware/OS 版本号}
```
---

### RK 方案

#### 关机动画名字为shutdownanimation.zip，制作动画方式跟开机动画一样；
#### 增加属性 

```
<integer name="config_shutdownAnimationDuration">1</integer>
```
添加到android\device\softwinner\tulip-p3\overlay\frameworks\base\core\res\res\values\config.xml
在文件android\device\softwinner\tulip-p3\tulip_p3.mk添加
```
# shutdownanimation

PRODUCT_COPY_FILES += \
    
 device/softwinner/tulip-p3/media/shutdownanimation.zip:system/media/shutdownanimation.zip 
```
### apk 签名
```
java -jar out/host/linux-x86/framework/signapk.jar build/target/product/security/platform.x509.pem build/target/product/security/platform.pk8 待签名.apk 签名后.apk
```