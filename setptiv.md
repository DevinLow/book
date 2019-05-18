### 如和添加自定义的系统默认属性
#### 1.添加默认字段 frameworks/base/packages/SettingsProvider/res/values/defaults.xml
```xml
    <integer name="def_bootlogo_show">0</integer>
```
#### 2.添加系统宏定义 frameworks/base/core/java/android/provider/Settings.java
```java
	public static final String BOOTLOGO_SHOW = "bootlogo_show";
```
#### 3.将自定义信息添加到数据库frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
```java
            loadIntegerSetting(stmt, Settings.System.BOOTLOGO_SHOW,
            			R.integer.def_bootlogo_show);
```
#### 4.读取方法
```java
	int v =Settings.System.getInt(resolver, Settings.System.BOOTLOGO_SHOW, 1);
```
#### 5.修改方法
```java
	Settings.System.putInt(getContentResolver(),Settings.System.BOOTLOGO_SHOW,value);
```
