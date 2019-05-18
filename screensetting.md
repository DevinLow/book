### 1.frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/PhoneStatusBar.java 
```java
//add devin 定义观察者
    final private ContentObserver screenshotShowObserver = new ContentObserver(mHandler) {
        @Override
        public void onChange(boolean selfChange) {
            boolean isShow = Settings.System.getInt(mContext.getContentResolver(), Settings.System.SCREENSHOT_BUTTON_SHOW, 1) == 1;
            ButtonDispatcher screenshotButton = mNavigationBarView.getScreenshotButton();
            screenshotButton.setVisibility(isShow ? View.VISIBLE : View.GONE);
        }
    };
//add end
//add devin 注册观察者
        mContext.getContentResolver().registerContentObserver(
                Settings.System.getUriFor(Settings.System.SCREENSHOT_BUTTON_SHOW), true,
                screenshotShowObserver);
//add end
```
### 2.Settings.System.SCREENSHOT_BUTTON_SHOW 默认值的做法请参考[添加自定义默认系统属性](./setptiv.md)    

### 3.在packages/apps/Settings/res/xml/display_settings.xml添加一个swith开关控件,在string资源中添加screenshot_show
```xml
<SwitchPreference
	android:key="screenshot_show"
	android:title="@string/screenshot_show" />
```

### 4.在packages/apps/Settings/src/com/android/settings/DisplaySettings.java 添加代码实现
```java
//定义
	private static final String KEY_SCREENSHOT_SHOW = "screenshot_show";
	private SwitchPreference mScreenshotPreference;
//注册onCreat 中注册
	mScreenshotPreference = (SwitchPreference) findPreference(KEY_SCREENSHOT_SHOW);
	boolean isShow = Settings.System.getInt(resolver, Settings.System.SCREENSHOT_BUTTON_SHOW, 1) == 1;
	if(mScreenshotPreference!=null){
		mScreenshotPreference.setChecked(isShow);
		mScreenshotPreference.setOnPreferenceChangeListener(new OnPreferenceChangeListener() {
		    @Override
		    public boolean onPreferenceChange(Preference preference, Object newValue) {
			boolean isOn=(Boolean)newValue;
			Settings.System.putInt(mContext.getContentResolver(),Settings.System.SCREENSHOT_BUTTON_SHOW,
				isOn ? 1 : 0);
			return true;
		    }
		});
	}
```
