# App crashes when WebView opens over SVG with ColorFilter on MediaTek Android 10 devices

## Summary
Flutter app crashes with SIGSEGV in libGLESv2_mtk.so when opening a WebView modal over an SVG that has a ColorFilter applied. This issue is specific to MediaTek devices running Android 10.

## Steps to Reproduce
1. Display an SVG using `SvgPicture.string()` with a `ColorFilter` applied
2. Open a WebView in a modal/overlay on top of the SVG
3. App crashes immediately on first launch after installation
4. On subsequent launches, WebView may work initially but crashes after several open/close cycles

Note: The crash only occurs when colorFilter is applied to the SVG. Without the ColorFilter, no crash seem to occur.

## Expected Behavior
WebView should open normally without causing the app to crash, regardless of underlying SVG with ColorFilter.

## Actual Behavior
App crashes with native SIGSEGV error in MediaTek's OpenGL ES library during EGL context operations.

## Crash Log
```
05-27 10:45:33.871  6662  6888 F libc    : Fatal signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x30 in tid 6888 (RenderThread), pid 6662 (_mtk_android_10)
05-27 10:45:34.055  7404  7404 F DEBUG   : *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
05-27 10:45:34.055  7404  7404 F DEBUG   : Build fingerprint: 'Redmi/angelican_global/angelican:10/QP1A.190711.020/V12.0.16.0.QCSMIXM:user/release-keys'
05-27 10:45:34.055  7404  7404 F DEBUG   : Revision: '0'
05-27 10:45:34.055  7404  7404 F DEBUG   : ABI: 'arm'
05-27 10:45:34.056  7404  7404 F DEBUG   : Timestamp: 2025-05-27 10:45:34+0200
05-27 10:45:34.056  7404  7404 F DEBUG   : pid: 6662, tid: 6888, name: RenderThread  >>> com.example.crash_libglesv2_mtk_android_10 <<<
05-27 10:45:34.056  7404  7404 F DEBUG   : uid: 10250
05-27 10:45:34.056  7404  7404 F DEBUG   : signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x30
05-27 10:45:34.056  7404  7404 F DEBUG   : Cause: null pointer dereference
05-27 10:45:34.056  7404  7404 F DEBUG   :     r0  00000000  r1  0000000c  r2  0000000c  r3  00000000
05-27 10:45:34.056  7404  7404 F DEBUG   :     r4  db716440  r5  b4618c00  r6  db709108  r7  c3644e58
05-27 10:45:34.056  7404  7404 F DEBUG   :     r8  b4618c00  r9  c3644e64  r10 00000000  r11 d370ba18
05-27 10:45:34.056  7404  7404 F DEBUG   :     ip  d2ce0ea8  sp  c3644e48  lr  d22d2a83  pc  d22d2a8c
05-27 10:45:34.163  7404  7404 F DEBUG   : 
05-27 10:45:34.163  7404  7404 F DEBUG   : backtrace:
05-27 10:45:34.163  7404  7404 F DEBUG   :       #00 pc 00088a8c  /vendor/lib/egl/libGLESv2_mtk.so (BuildId: 34ed5df2075eeb41cbc04b3113ec2bda)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #01 pc 000d4b69  /vendor/lib/egl/libGLESv2_mtk.so (BuildId: 34ed5df2075eeb41cbc04b3113ec2bda)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #02 pc 00026cb7  /vendor/lib/egl/libGLESv2_mtk.so (BuildId: 34ed5df2075eeb41cbc04b3113ec2bda)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #03 pc 00011883  /vendor/lib/libIMGegl.so (IMGeglMakeCurrent+3026) (BuildId: 371891638abe6c30816cd11ab5761699)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #04 pc 0001c5af  /vendor/lib/egl/libGLES_meow.so (BuildId: 6d6caf55643460f2db471f5c3405e207)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #05 pc 0000f8c7  /system/lib/libEGL.so (android::egl_display_t::makeCurrent(android::egl_context_t*, android::egl_context_t*, void*, void*, void*, void*, void*, void*)+170) (BuildId: ba8fdea35c7f0366c4bc3b056057dc3a)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #06 pc 00015ccb  /system/lib/libEGL.so (android::eglMakeCurrentImpl(void*, void*, void*, void*)+318) (BuildId: ba8fdea35c7f0366c4bc3b056057dc3a)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #07 pc 002a01dd  /system/lib/libhwui.so (android::uirenderer::renderthread::EglManager::makeCurrent(void*, int*, bool)+64) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #08 pc 002a0175  /system/lib/libhwui.so (android::uirenderer::skiapipeline::SkiaOpenGLPipeline::makeCurrent()+28) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.163  7404  7404 F DEBUG   :       #09 pc 002a457f  /system/lib/libhwui.so (android::uirenderer::renderthread::CanvasContext::makeCurrent()+30) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #10 pc 002a442d  /system/lib/libhwui.so (android::uirenderer::renderthread::DrawFrameTask::syncFrameState(android::uirenderer::TreeInfo&)+68) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #11 pc 002a40ef  /system/lib/libhwui.so (_ZNSt3__110__function6__funcIZN7android10uirenderer12renderthread13DrawFrameTask11postAndWaitEvE3$_0NS_9allocatorIS6_EEFvvEEclEv$c303f2d2360db58ed70a2d0ac7ed911b+78) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #12 pc 002b1b8f  /system/lib/libhwui.so (android::uirenderer::WorkQueue::process()+158) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #13 pc 002b19ed  /system/lib/libhwui.so (android::uirenderer::renderthread::RenderThread::threadLoop()+72) (BuildId: 68f93a2184251a781de38b9998ae91e3)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #14 pc 0000d8b3  /system/lib/libutils.so (android::Thread::_threadLoop(void*)+182) (BuildId: a4424e73dbe004e8623d6713ad30a604)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #15 pc 000a6293  /apex/com.android.runtime/lib/bionic/libc.so (__pthread_start(void*)+20) (BuildId: af31ec96b35fedca378e07659b30896c)
05-27 10:45:34.164  7404  7404 F DEBUG   :       #16 pc 00060803  /apex/com.android.runtime/lib/bionic/libc.so (__start_thread+30) (BuildId: af31ec96b35fedca378e07659b30896c)
```

## Device Information
- **Device**: Xiaomi Redmi 9C (MediaTek chipset)
- **OS**: Android 10
- **Build**: QP1A.190711.020/V12.0.16.0.QCSMIXM

## Flutter Information
```
[!] Flutter (Channel [user-branch], 3.29.3, on macOS 15.3.2 24D81 darwin-arm64, locale sv-SE) [993ms]
    ! Flutter version 3.29.3 on channel [user-branch] at /Users/_/Dev/.flutter
      Currently on an unknown channel. Run `flutter channel` to switch to an official channel.
      If that doesn't fix the issue, reinstall Flutter by following instructions at https://flutter.dev/setup.
    ! Upstream repository unknown source is not the same as FLUTTER_GIT_URL
    • FLUTTER_GIT_URL = https://github.com/flutter/flutter.git
    • Framework revision ea121f8859 (7 weeks ago), 2025-04-11 19:10:07 +0000
    • Engine revision cf56914b32
    • Dart version 3.7.2
    • DevTools version 2.42.3
    • If those were intentional, you can disregard the above warnings; however it is recommended to use "git" directly to perform update checks and upgrades.

[✓] Android toolchain - develop for Android devices (Android SDK version 35.0.1) [3,3s]
    • Android SDK at /Users/_/Library/Android/sdk
    • Platform android-35, build-tools 35.0.1
    • ANDROID_HOME = /Users/_/Library/Android/sdk
    • Java binary at: /Applications/Android Studio.app/Contents/jbr/Contents/Home/bin/java
      This is the JDK bundled with the latest Android Studio installation on this machine.
      To manually set the JDK path, use: `flutter config --jdk-dir="path/to/jdk"`.
    • Java version OpenJDK Runtime Environment (build 21.0.6+-13368085-b895.109)
    • All Android licenses accepted.

[✓] Android Studio (version 2024.3) [7ms]
    • Android Studio at /Applications/Android Studio.app/Contents
    • Flutter plugin can be installed from:
      🔨 https://plugins.jetbrains.com/plugin/9212-flutter
    • Dart plugin can be installed from:
      🔨 https://plugins.jetbrains.com/plugin/6351-dart
    • Java version OpenJDK Runtime Environment (build 21.0.6+-13368085-b895.109)

[✓] VS Code (version 1.100.2) [6ms]
    • VS Code at /Applications/Visual Studio Code.app/Contents
    • Flutter extension version 3.110.0

[✓] Connected device (4 available) [6,1s]
    • M2006C3MNG (mobile)             • QOFELZHIX4XCNJ9L      • android-arm    • Android 10 (API 29)

[✓] Network resources [1 022ms]
    • All expected network resources are available.
```

## Minimal Reproducible Example
Complete minimal reproduction project available at:

https://github.com/rckrdstrgrd/flutter_crash_libglesv2_mtk_android_10

Consistently reproducible on MediaTek Android 10 devices
Only occurs on **MediaTek devices running Android 10**

- **Video**: https://github.com/user-attachments/assets/6c79032f-7b45-4e1c-a148-d23d3b9e7093
- **Screenshots Device info**:
https://github.com/user-attachments/assets/ef3d26c2-c68a-48bf-ab6d-1249e316efaa
https://github.com/user-attachments/assets/9b1e58a2-70cc-49d1-b668-a75050d3de33

## Possibly Related Issues
- https://github.com/flutter/flutter/issues/114486
- https://github.com/flutter/flutter/issues/132562
- https://github.com/flutter/flutter/issues/103498
- https://github.com/flutter/flutter/issues/106160
