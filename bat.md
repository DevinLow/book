### 隐藏systemUI 电量图标
### frameworks/base/packages/SystemUI/src/com/android/systemui/BatteryMeterView.java
```java
    @Override
    public void onTuningChanged(String key, String newValue) {
        if (StatusBarIconController.ICON_BLACKLIST.equals(key)) {
            ArraySet<String> icons = StatusBarIconController.getIconBlacklist(newValue);
           // setVisibility(icons.contains(mSlotBattery) ? View.GONE : View.VISIBLE);
            setVisibility(View.GONE );//隐藏电量图标
        }
    }
```

