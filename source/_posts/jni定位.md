---
title: jni定位
date: 2019-07-31 21:19:33
tags:
- Android
- jni 
---
```
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: Build fingerprint: 'HiDPT/Hi3751V811_DMO/Hi3751V811:8.0.0/OPR6.170623.013/iipjen07310959:user/release-keys'
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: Revision: '0'
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: ABI: 'arm64'
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: pid: 1104, tid: 1104, name: utmethod.pinyin  >>> com.android.inputmethod.pinyin <<<
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG: signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x4cbe05113242
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x0   0000006ffe158100  x1   00004cbe05113242  x2   0000000000000002  x3   0000006fff0f9c88
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x4   00000000000005aa  x5   0000007fc5597b54  x6   0000000000000000  x7   0000000000000073
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x8   0000000000000002  x9   0000007fc5597438  x10  0000006fff0f9800  x11  0000000000000020
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x12  0000000000000002  x13  0000000000000003  x14  0000000000008c01  x15  0000000000000003
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x16  0000006ff3337850  x17  0000006ff33198c8  x18  0000000000000139  x19  0000000000000001
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x20  0000006ffe158100  x21  0000000000000000  x22  0000000000000000  x23  0000000000000000
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x24  000000000000000a  x25  0000000000000002  x26  00004cbe05113242  x27  0000007fc5596df8
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     x28  0000007fc5597438  x29  0000007fc5597ad0  x30  0000006ff3319940
2019-07-31 21:09:34.697 1151-1151/? A/DEBUG:     sp   0000007fc5596da0  pc   0000006ff33154b0  pstate 0000000080000000
2019-07-31 21:09:34.698 1151-1151/? A/DEBUG: backtrace:
2019-07-31 21:09:34.698 1151-1151/? A/DEBUG:     #00 pc 00000000000104b0  /data/app/com.android.inputmethod.pinyin-T8RmRV3m7olzZhUmBZJinA==/lib/arm64/libjni_pinyinime.so (_ZN10ime_pinyin8DictTrie8get_lpisEPKttPNS_10LmaPsbItemEm+152)
2019-07-31 21:09:34.698 1151-1151/? A/DEBUG:     #01 pc 000000000001493c  /data/app/com.android.inputmethod.pinyin-T8RmRV3m7olzZhUmBZJinA==/lib/arm64/libjni_pinyinime.so (_ZN10ime_pinyin12MatrixSearch8get_lpisEPKtmPNS_10LmaPsbItemEmS2_b+116)
```

## jni定位方法 
	cd 进入报错的so文件目录 然后输入  --  不是libs下的so文件 是lib同层下的obj下的so文件 -- 工位上板卡是abi架构

```
~/Library/Android/sdk/ndk/android-ndk-r14b/toolchains/aarch64-linux-android-4.9/prebuilt/darwin-x86_64/bin/aarch64-linux-android-addr2line -e libjni_pinyinime.so 00000000000104b0
```

## 没看到so文件 
	cd进对应cpp代码的目录 -- git根目录 
	输入 /Users/lei/Library/Android/sdk/ndk-bundle/ndk-build 生成so文件
