### 一 添加资源及文件
#### 1.frameworks/base/packages/SystemUI/res/layout/volume_sub.xml
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
    android:id="@+id/volume_sub"
    android:layout_width="@dimen/navigation_key_width"
    android:layout_height="match_parent"
    android:layout_weight="0"
    android:src="@drawable/ic_sysbar_volume_down"
    systemui:keyCode="25"
    android:scaleType="center"
    android:contentDescription="@string/accessibility_volume_sub"
    android:paddingStart="@dimen/navigation_key_padding"
    android:paddingEnd="@dimen/navigation_key_padding"
    />

```
#### 2.frameworks/base/packages/SystemUI/res/layout/volume_add.xml
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
    android:id="@+id/volume_add"
    android:layout_width="@dimen/navigation_key_width"
    android:layout_height="match_parent"
    android:layout_weight="0"
    android:src="@drawable/ic_sysbar_volume_add"
    systemui:keyCode="24"
    android:scaleType="center"
    android:contentDescription="@string/accessibility_volume_add"
    android:paddingStart="@dimen/navigation_key_padding"
    android:paddingEnd="@dimen/navigation_key_padding"
    />
```
#### 3.修改NAVBAR布局配置frameworks/base/packages/SystemUI/res/values/config.xml
```xml
<string name="config_navBarLayout" translatable="false">space;volume_sub,volume_add,recent,home,back;menu_ime</string>
```
#### 4.添加string资源frameworks/base/packages/SystemUI/res/values/strings.xml
```xml
    <!--add devin-->
    <string name="accessibility_volume_add">Add</string>
    <string name="accessibility_volume_sub">Sub</string>
```
#### 5. 在drawable目录中添加对应图标
#### 6. 将volume按钮的布局添加到导航栏frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/NavigationBarInflaterView.java
```java
//定义
    public static final String VOLUME_ADD = "volume_add";
    public static final String VOLUME_SUB = "volume_sub";
//添加到容器
//@Nullable
//    protected View inflateButton(String buttonSpec, ViewGroup parent, boolean landscape,
//            int indexInParent) {
 	else if (VOLUME_ADD.equals(button)) {
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
#### 7.初始化 frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/NavigationBarView.java
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
        mButtonDisatchers.put(R.id.volume_add, new ButtonDispatcher(R.id.volume_add));
        mButtonDisatchers.put(R.id.volume_sub, new ButtonDispatcher(R.id.volume_sub));
		//add end
    }
//add devin
    public ButtonDispatcher getVolumeAddButton() {
        return mButtonDisatchers.get(R.id.volume_add);
    }

    public ButtonDispatcher getVolumeSubButton() {
        return mButtonDisatchers.get(R.id.volume_sub);
    }
//add end
```
#### 8.frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/PhoneStatusBar.java
```java
//   private void prepareNavigationBarView() {
	ButtonDispatcher volumeAddButton=mNavigationBarView.getVolumeAddButton();
        ButtonDispatcher volumeSubButton=mNavigationBarView.getVolumeSubButton();
	//ro.maozen.systembar.voiceicon 此属性在product mk中定义，true显示，false 不显示
        boolean isShowVolumeButton="true".equals(SystemProperties.get("ro.maozen.systembar.voiceicon","true"));
        if(isShowVolumeButton){
            volumeAddButton.setVisibility(View.VISIBLE);
            volumeSubButton.setVisibility(View.VISIBLE);
        }else{
            volumeAddButton.setVisibility(View.GONE);
            volumeSubButton.setVisibility(View.GONE);
        }
        if (mContext.getResources().getConfiguration().smallestScreenWidthDp < 400) {
            volumeAddButton.setVisibility(View.GONE);
            volumeSubButton.setVisibility(View.GONE);
        }
```

