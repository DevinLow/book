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

### 左右声道

```
--- a/sound/soc/codecs/rk3026_codec.c
++ b/sound/soc/codecs/rk3026_codec.c
-1433,7 +1433,7 static int rk3026_set_dai_fmt(struct snd_soc_dai *codec_dai,
snd_soc_update_bits(codec, RK3026_ADC_INT_CTL2,RK3026_ABCLK_POL_MASK | RK3026_I2S_MODE_MASK, adc_aif2);
snd_soc_update_bits(codec, RK3026_DAC_INT_CTL1,- RK3026_DLRCK_POL_MASK | RK3026_DAC_DF_MASK, dac_aif1);
RK3026_DLRCK_POL_MASK | RK3026_DAC_DF_MASK, dac_aif1 | RK3026_DLRCK_POL_EN);
snd_soc_update_bits(codec, RK3026_DAC_INT_CTL2,RK3026_DBCLK_POL_MASK, dac_aif2);
```    
    
### 关闭setting中screenshot 项
```
//if(!SystemProperties.get("ro.build.characteristics","null").equals("tablet")) {
       setTileEnabled(new ComponentName(packageName,
                   Settings.ScreenshotSettingsActivity.class.getName()),
                   false, isAdmin, pm);
//  }
 ```

### 关机LOGO  播放LOOP(1)问题

```
diff --git a/services/core/java/com/android/server/power/ShutdownThread.java b/services/core/java/com/android/server/power/ShutdownThread.java
index 6db8461..de53dbe 100755
--- a/services/core/java/com/android/server/power/ShutdownThread.java
+++ b/services/core/java/com/android/server/power/ShutdownThread.java
@ -797,9 +797,15 @ public final class ShutdownThread extends Thread {

//Wait until the animation loop finished
     private static void wait_shutdownanim_end() {
+        int count = 0;
         while(!SystemProperties.get(LOOP_COMPLETED_PROP_NAME, "false").equals("true")) {
             try {
                 Thread.sleep(200);
+                count++;
+                if(count > 10){
+                    Log.e(TAG, "add by huangkaihui");
+                    SystemProperties.set("service.bootanim.exit", "1");
		     break;
+                }
             } catch (Exception e) {
             }
         }
```   

---
### allwinner 方案
    
```
pack4dist -v,要求输入4次1  全志加密固件
```   
### Settings 删除顶层item 蓝牙 eth
```xml
AndroidManifest.xml
<!--
<meta-data android:name="com.android.settings.category"
android:value="com.android.settings.category.wireless" />
<meta-data android:name="com.android.settings.FRAGMENT_CLASS"
android:value="com.android.settings.bluetooth.BluetoothSettings" />
-->
<!--
    <meta-data android:name="com.android.settings.category"
	android:value="com.android.settings.category.wireless" />
    <meta-data android:name="com.android.settings.FRAGMENT_CLASS"
	android:value="com.android.settings.ethernet.EthernetSettings" />
-->
Settings/src/com/android/settings/SettingsActivity.java
//setTileEnabled(new ComponentName(packageName,
// 		 Settings.EthernetSettingsActivity.class.getName()),
//               pm.hasSystemFeature(PackageManager.FEATURE_ETHERNET), isAdmin, pm);
//setTileEnabled(new ComponentName(packageName,
//		Settings.BluetoothSettingsActivity.class.getName()),
//		pm.hasSystemFeature(PackageManager.FEATURE_BLUETOOTH), isAdmin, pm);

```
#### boot setup default Launcher 

##### 1.add intent category in app mainfest

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.HOME" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.maozen.home" />
    <!-- "android.intent.category.maozen.home" this is the sample category，
    use it diffrent to others Launcher -->
</intent-filter>
```

###### 2. modify frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java

```java
    Intent getHomeIntent() {
        Intent intent = new Intent(mTopAction, mTopData != null ? Uri.parse(mTopData) : null);
        intent.setComponent(mTopComponent);
        intent.addFlags(Intent.FLAG_DEBUG_TRIAGED_MISSING);
        if (mFactoryTest != FactoryTest.FACTORY_TEST_LOW_LEVEL) {
            intent.addCategory(Intent.CATEGORY_HOME);
        }
        return intent;
    }
    /*
    * our Intent addCategory
    */
    Intent getMaozenHomeIntent() {
        Intent intent = new Intent(mTopAction, mTopData != null ? Uri.parse(mTopData) : null);
        intent.setComponent(mTopComponent);
        intent.addFlags(Intent.FLAG_DEBUG_TRIAGED_MISSING);
        if (mFactoryTest != FactoryTest.FACTORY_TEST_LOW_LEVEL) {
        intent.addCategory("android.intent.category.maozen.home");
            intent.addCategory(Intent.CATEGORY_HOME);
        }
        return intent;
    }

    boolean startHomeActivityLocked(int userId, String reason) {
        if (mFactoryTest == FactoryTest.FACTORY_TEST_LOW_LEVEL
                && mTopAction == null) {
            // We are running in factory test mode, but unable to find
            // the factory test app, so just sit around displaying the
            // error message and don't try to start anything.
            return false;
        }
            //filter ouer Launcher first
        Intent intent = getMaozenHomeIntent();
        ActivityInfo aInfo = resolveActivityInfo(intent, STOCK_PM_FLAGS, userId);
        //then if null，filter system default Launcher 
        if(aInfo==null){
        intent = getHomeIntent();
            aInfo = resolveActivityInfo(intent, STOCK_PM_FLAGS, userId);
        }
        if (aInfo != null) {
            intent.setComponent(new ComponentName(aInfo.applicationInfo.packageName, aInfo.name));
            // Don't do this if the home app is currently being
            // instrumented.
            aInfo = new ActivityInfo(aInfo);
            aInfo.applicationInfo = getAppInfoForUser(aInfo.applicationInfo, userId);
            ProcessRecord app = getProcessRecordLocked(aInfo.processName,
                    aInfo.applicationInfo.uid, true);
            if (app == null || app.instrumentationClass == null) {
                intent.setFlags(intent.getFlags() | Intent.FLAG_ACTIVITY_NEW_TASK);
                mActivityStarter.startHomeActivityLocked(intent, aInfo, reason);
            }
        } else {
            Slog.wtf(TAG, "No home screen found for " + intent, new Throwable());
        }

        return true;
    }
```


