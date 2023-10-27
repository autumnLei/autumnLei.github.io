---
title: Android开发需要注意的一些规范
date: 2023-10-26 20:26:14
tags:
- Android
- 开发规范
---

>【建议】在Activity中显示对话框或弹出浮层时，尽量使用DialogFragment，而非Dialog/AlertDialog，这样便于随Activity生命周期管理对话框/弹出浮层的生命周期。

>【推荐】文本大小使用单位dp，view大小使用单位dp。对于Textview，如果在文字大小确定的情况下推荐使用wrap_content布局避免出现文字显示不全的适配问题。
说明：之所以文本大小也推荐使用dp而非sp，因为sp是Android早期推荐使用的，但其实sp不仅和dp一样受屏幕密度的影响，还受到系统设置里字体大小的影响，所以使用dp对于应用开发会更加保证UI的一致性和还原度。

>【推荐】在需要时刻刷新某一区域的组件时，建议通过以下方式避免引发全局layout刷新:
> 1. 设置固定的view大小的高宽，如倒计时组件等；
> 2. 调用view的layout方式修改位置，如弹幕组件等;
> 3. 通过修改canvas位置并且调用invalidate(int l, int t, int r, int b)等方式限定刷新区域；
> 4. 通过设置一个是否允许requestLayout的变量，然后重写控件的requestlayout、onSizeChanged方法，判断控件的大小没有改变的情况下，当进入requestLayout的时候，直接返回而不调用super的requestLayout方法。

>【强制】在Application的业务初始化代码加入进程判断，确保只在自己需要的进程初始化。特别是后台进程减少不必要的业务初始化。

> 【建议】新建线程时，定义能识别自己业务的线程名称，便于性能优化和问题排查。

> 【强制】应用间共享文件时，不要通过放宽文件系统权限的方式去实现，而应使用FileProvider。

> 【强制】执行SQL语句时，应使用SQLiteDatabase#insert()、update()、delete()，不要使用SQLiteDatabase#execSQL()，以免SQL注入风险。

> 【参考】大图片资源不要直接打包到apk，可以考虑通过文件仓库远程下载，减小包体积。

> 【强制】WebView 应设置 WebView#getSettings()#setAllowFileAccess(false)、WebView#getSettings()#setAllowFileAccessFromFileURLs(false)、WebView#getSettings()#setAllowUniversalAccessFromFileURLs(false)，阻止 file scheme URL 的访问。

> 【强制】Android APP在HTTPS通信中，验证策略需要改成严格模式。
说明：Android APP在HTTPS通信中，使用ALLOW_ALL_HOSTNAME_VERIFIER，表示允许和所有的HOST建立SSL通信，这会存在中间人攻击的风险，最终导致敏感信息可能会被劫持，以及其他形式的攻击。