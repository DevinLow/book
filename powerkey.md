###  定制长按 power 按键功能
#### 1，功能配置/frameworks/base/core/res/res/values/config.xml
```xml
    <string-array translatable="false" name="config_globalActionsList">
        <item>power</item>
        <item>restart</item>
	<!--添加我们需要的栏目-->
	<item>home</item>
        <item>bugreport</item>
        <item>users</item>
    </string-array>
```
#### 2，添加相关资源frameworks/base/core/res/res/values/symbols.xml
```xml
  <java-symbol type="string" name="global_action_home" />
  <string name="global_action_home">"Home"</string>
  <java-symbol type="drawable" name="ic_home_black_24dp" />
```
#### 3,添加home SVG 图标/frameworks/base/core/res/res/drawable/ic_home_black_24dp.xml
```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="24dp"
        android:height="24dp"
        android:viewportWidth="24.0"
        android:viewportHeight="24.0">
    <path
        android:fillColor="#FF000000"
        android:pathData="M10,20v-6h4v6h5v-8h3L12,3 2,12h3v8z"/>
</vector>
```
#### 4,framework res 部分添加资源，需要更新api
```shell
make update-api
make -j12
```
#### 5，在代码中添加我们的定制内容frameworks/base/services/core/java/com/android/server/policy/GlobalActions.java
```java
//添加key   
private static final String GLOBAL_ACTION_KEY_HOME = "home";
//实现自定义item
private final class HomeAction extends SinglePressAction implements LongPressAction{

        private HomeAction() {
            super(R.drawable.ic_home_black_24dp, R.string.global_action_home);
        }

        @Override
        public boolean showDuringKeyguard() {
            return true;
        }

        @Override
        public boolean showBeforeProvisioning() {
            return true;
        }

        @Override
        public boolean onLongPress() {
            return true;
        }

        @Override
        public void onPress() {
            mHandler.post(new Runnable() {
                @Override
                public void run() {
                    Intent intent = new Intent();
                    intent.setClassName("devin.carviewpager","devin.carviewpager.MainActivity");
                    mContext.startActivity(intent);
                }
            });
        }
    }
//将实现的item 添加到列表中
//private GlobalActionsDialog createDialog() 
            if (GLOBAL_ACTION_KEY_POWER.equals(actionKey)) {
                mItems.add(new PowerAction());
            } else if (GLOBAL_ACTION_KEY_AIRPLANE.equals(actionKey)) {
                mItems.add(mAirplaneModeOn);
            } else if (GLOBAL_ACTION_KEY_BUGREPORT.equals(actionKey)) {
                if (Settings.Global.getInt(mContext.getContentResolver(),
                        Settings.Global.BUGREPORT_IN_POWER_MENU, 0) != 0 && isCurrentUserOwner()) {
                    mItems.add(new BugReportAction());
                }
            } else if (GLOBAL_ACTION_KEY_SILENT.equals(actionKey)) {
                if (mShowSilentToggle) {
                    mItems.add(mSilentModeAction);
                }
            } else if (GLOBAL_ACTION_KEY_USERS.equals(actionKey)) {
                if (SystemProperties.getBoolean("fw.power_user_switcher", false)) {
                    addUsersToMenu(mItems);
                }
            } else if (GLOBAL_ACTION_KEY_SETTINGS.equals(actionKey)) {
                mItems.add(getSettingsAction());
            } else if (GLOBAL_ACTION_KEY_LOCKDOWN.equals(actionKey)) {
                mItems.add(getLockdownAction());
            } else if (GLOBAL_ACTION_KEY_VOICEASSIST.equals(actionKey)) {
                mItems.add(getVoiceAssistAction());
            } else if (GLOBAL_ACTION_KEY_ASSIST.equals(actionKey)) {
                mItems.add(getAssistAction());
            } else if (GLOBAL_ACTION_KEY_RESTART.equals(actionKey)) {
                mItems.add(new RestartAction());
            }else if (GLOBAL_ACTION_KEY_HOME.equals(actionKey)) { //add for home button,add by Devin
                mItems.add(new HomeAction());
            } else {
                Log.e(TAG, "Invalid global action key " + actionKey);
            }
            // Add here so we don't add more than one.
            addedKeys.add(actionKey);
        }
```
#### 6,如果需要实现全屏长按powerkey 弹出效果，需要利用Activity 方式实现。在此文件中添加流程。
