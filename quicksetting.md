### 关闭下拉显示quicksetting 功能
```java
    @Override
    public boolean onTouchEvent(MotionEvent event) {
	//add devin 屏蔽下拉quiksetting
/*        boolean barConsumedEvent = mBar.interceptTouchEvent(event);

        if (DEBUG_GESTURES) {
            if (event.getActionMasked() != MotionEvent.ACTION_MOVE) {
                EventLog.writeEvent(EventLogTags.SYSUI_PANELBAR_TOUCH,
                        event.getActionMasked(), (int) event.getX(), (int) event.getY(),
                        barConsumedEvent ? 1 : 0);
            }
        }

        return barConsumedEvent || super.onTouchEvent(event);
*/
	return true; 
    }
```
