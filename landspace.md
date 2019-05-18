### 修改窗口管理服务，应用请求时候，只支持横屏。文件路径/home/devin/workspace/newworld/android/frameworks/base/services/core/java/com/android/server/wm/WindowManagerService.java
```java
    boolean updateOrientationFromAppTokensLocked(boolean inTransaction) {
        long ident = Binder.clearCallingIdentity();
        try {
	    //add devin for system SCREEN_ORIENTATION_LANDSCAPE only
            //int req = getOrientationLocked();
	    int req = ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE;
            if (req != mLastOrientation) {
                mLastOrientation = req;
                //send a message to Policy indicating orientation change to take
                //action like disabling/enabling sensors etc.,
                mPolicy.setCurrentOrientationLw(req);
                if (updateRotationUncheckedLocked(inTransaction)) {
                    // changed
                    return true;
                }
            }

            return false;
        } finally {
            Binder.restoreCallingIdentity(ident);
        }
    }
```
