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
