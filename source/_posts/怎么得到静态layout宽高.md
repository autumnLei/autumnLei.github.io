---
title: 怎么得到静态layout宽高
date: 2019-05-05 15:13:29
tags:
- Android
---
比如要得到一个linearlayout的，可以在setContentView或者inflate后获取
```      
final LinearLayout linearLayout = view.findViewById(R.id.linearlayout_left);
linearLayout.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
    @Override
    public void onGlobalLayout() {
        RLog.d(TAG, "onGlobalLayout: "+linearLayout.getWidth());
    }
});
```
