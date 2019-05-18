### 一 添加资源及文件
#### 1.frameworks/base/packages/SystemUI/res/layout/screenshot.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- Copyright (C) 2016 The Android Open Source Project

     Licensed under the Apache License, Version 2.0 (the "License");
     you may not use this file except in compliance with the License.
     You may obtain a copy of the License at

          http://www.apache.org/licenses/LICENSE-2.0

     Unless required by applicable law or agreed to in writing, software
     distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and
     limitations under the License.
-->
<com.android.systemui.statusbar.policy.KeyButtonView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:systemui="http://schemas.android.com/apk/res-auto"
    android:id="@+id/screenshot"
    android:layout_width="@dimen/navigation_key_width"
    android:layout_height="match_parent"
    android:layout_weight="0"
    android:src="@drawable/capture"
    systemui:keyCode="0"
    android:scaleType="center"
    android:contentDescription="@string/accessibility_screenshot"
    android:paddingStart="@dimen/navigation_key_padding"
    android:paddingEnd="@dimen/navigation_key_padding"
    />

```
#### 2.修改NAVBAR布局配置frameworks/base/packages/SystemUI/res/values/config.xml
```xml
 <string name="config_navBarLayout" translatable="false">space;screenshot,volume_sub,volume_add,recent,home,back;menu_ime</string>
```
#### 3.添加string资源frameworks/base/packages/SystemUI/res/values/strings.xml
```xml
    <!--add devin-->
    <string name="accessibility_screenshot">Screenshot</string>
```
#### 4. 在drawable目录中添加对应图标
#### 5. 将screenshot按钮的布局添加到导航栏frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/NavigationBarInflaterView.java
```java
//定义
    public static final String SCREENSHOT = "screenshot";
//添加到容器
//@Nullable
//    protected View inflateButton(String buttonSpec, ViewGroup parent, boolean landscape,
//            int indexInParent) {
 	else if (SCREENSHOT.equals(button)) { //add devin
            v = inflater.inflate(R.layout.screenshot, parent, false);
            if (landscape && isSw600Dp()) {
                setupLandButton(v);
            }else if (VOLUME_ADD.equals(button)) {
            v = inflater.inflate(R.layout.volume_add, parent, false);
            if (landscape && isSw600Dp()) {
                setupLandButton(v);
            }
        } else if (VOLUME_SUB.equals(button)) {
            v = inflater.inflate(R.layout.volume_sub, parent, false);
            if (landscape && isSw600Dp()) {
                setupLandButton(v);
            }  //add end
        } else if (MENU_IME.equals(button)) {
            v = inflater.inflate(R.layout.menu_ime, parent, false);
        } else if (NAVSPACE.equals(button)) {
```
#### 6.初始化 frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/NavigationBarView.java
```java
    public NavigationBarView(Context context, AttributeSet attrs) {
        super(context, attrs);

        mDisplay = ((WindowManager) context.getSystemService(
                Context.WINDOW_SERVICE)).getDefaultDisplay();

        mVertical = false;
        mShowMenu = false;
        mGestureHelper = new NavigationBarGestureHelper(context);

        mConfiguration = new Configuration();
        mConfiguration.updateFrom(context.getResources().getConfiguration());
        updateIcons(context, Configuration.EMPTY, mConfiguration);

        mBarTransitions = new NavigationBarTransitions(this);

        mButtonDisatchers.put(R.id.back, new ButtonDispatcher(R.id.back));
        mButtonDisatchers.put(R.id.home, new ButtonDispatcher(R.id.home));
        mButtonDisatchers.put(R.id.recent_apps, new ButtonDispatcher(R.id.recent_apps));
        mButtonDisatchers.put(R.id.menu, new ButtonDispatcher(R.id.menu));
        mButtonDisatchers.put(R.id.ime_switcher, new ButtonDispatcher(R.id.ime_switcher));
		//add devin
        mButtonDisatchers.put(R.id.screenshot, new ButtonDispatcher(R.id.screenshot));
		//add end
    }
//add devin
    public ButtonDispatcher getScreenshotButton() {
        return mButtonDisatchers.get(R.id.screenshot);
    }
//add end
```
#### 7.添加系统默认属性Settings.System.SCREENSHOT_BUTTON_SHOW，实现参考[添加自定义默认系统属性](./setptiv.md)  
#### 8.frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/PhoneStatusBar.java
```java
//   private void prepareNavigationBarView() {
        ButtonDispatcher screenshotButton=mNavigationBarView.getScreenshotButton();
        screenshotButton.setOnClickListener(mScreenshotClickListener);
        screenshotButton.setOnTouchListener(mScreenshotTouchListener);
        screenshotButton.setVisibility(View.VISIBLE);
        boolean isShow=Settings.System.getInt(mContext.getContentResolver(), Settings.System.SCREENSHOT_BUTTON_SHOW, 1)==1;
        if(isShow){
            screenshotButton.setVisibility(View.VISIBLE);
        }else{
            screenshotButton.setVisibility(View.GONE);
        }
```
#### 9.添加截屏动作实现frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/PhoneStatusBar.java
```java
	//add devin
    private View.OnClickListener mScreenshotClickListener = new View.OnClickListener() {
        public void onClick(View v) {
           // Intent intent=new Intent("android.intent.action.SCREENSHOT");
          //  mContext.sendBroadcast(intent);
            mHandler.post(mScreenshotRunnable);
        }
    };

    private View.OnTouchListener mScreenshotTouchListener = new View.OnTouchListener() {

        @Override
        public boolean onTouch(View v, MotionEvent event) {
            // TODO Auto-generated method stub
            if (event.getAction() == MotionEvent.ACTION_UP) {
               // Intent intent = new Intent("android.intent.action.SCREENSHOT");
               // mContext.sendBroadcast(intent);
                mHandler.post(mScreenshotRunnable);
            }
            return false;
        }
    };
    final Object mScreenshotLock = new Object();
    ServiceConnection mScreenshotConnection = null;

    final Runnable mScreenshotTimeout = new Runnable() {
        @Override public void run() {
            synchronized (mScreenshotLock) {
                if (mScreenshotConnection != null) {
                    mContext.unbindService(mScreenshotConnection);
                    mScreenshotConnection = null;
                }
            }
        }
    };

    // Assume this is called from the Handler thread.
    private void takeScreenshot() {
        synchronized (mScreenshotLock) {
            if (mScreenshotConnection != null) {
                return;
            }
            ComponentName cn = new ComponentName("com.android.systemui",
                    "com.android.systemui.screenshot.TakeScreenshotService");
            Intent intent = new Intent();
            intent.setComponent(cn);
            ServiceConnection conn = new ServiceConnection() {
                @Override
                public void onServiceConnected(ComponentName name, IBinder service) {
                    synchronized (mScreenshotLock) {
                        if (mScreenshotConnection != this) {
                            return;
                        }
                        Messenger messenger = new Messenger(service);
                        Message msg = Message.obtain(null, 1);
                        final ServiceConnection myConn = this;
                        Handler h = new Handler(mHandler.getLooper()) {
                            @Override
                            public void handleMessage(Message msg) {
                                synchronized (mScreenshotLock) {
                                    if (mScreenshotConnection == myConn) {
                                        mContext.unbindService(mScreenshotConnection);
                                        mScreenshotConnection = null;
                                        mHandler.removeCallbacks(mScreenshotTimeout);
                                    }
                                }
                            }
                        };
                        msg.replyTo = new Messenger(h);
                        msg.arg1 = msg.arg2 = 0;
                        msg.arg1 = 1;
                        msg.arg2 = 1;
                        try {
                            messenger.send(msg);
                        } catch (RemoteException e) {
                        }
                    }
                }
                @Override
                public void onServiceDisconnected(ComponentName name) {}
            };
            if (mContext.bindServiceAsUser(
                    intent, conn, Context.BIND_AUTO_CREATE, UserHandle.CURRENT)) {
                mScreenshotConnection = conn;
                mHandler.postDelayed(mScreenshotTimeout, 10000);
            }
        }
    }

    private final Runnable mScreenshotRunnable = new Runnable() {
        @Override
        public void run() {
            takeScreenshot();
        }
    };
	//add end
```

