## **Android 笔记**
### **编码显示Android firmware version 之类的信息**
```java
import android.os.Build;
import android.os.Build.VERSION_CODES;

TextView.setText(Build.VERSION.SDK);//SDK版本号 
TextView.setText(Build.VERSION.RELEASE);//Firmware/OS 版本号}
```
---