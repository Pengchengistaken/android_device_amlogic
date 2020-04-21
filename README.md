# android_device_amlogic
Device configuration for building TWRP on Amlogic reference boards

## Build notes:
- To get started with OMNI sources to build TWRP, check out the minimal manifest repo [here](https://github.com/minimal-manifest-twrp/platform_manifest_twrp_omni).
- To save space, a shallow clone is recommended.
- These device configurations should be built with the **twrp-9.0** branch.
- Copy this project to /device/amlogic/ directory.

## More tips:
- To add busybox repository, add it to omni-default.xml
```git
diff --git a/omni-default.xml b/omni-default.xml
index 0b3ed04..3a06465 100644
--- a/omni-default.xml
+++ b/omni-default.xml
@@ -28,6 +28,9 @@
   <project path="external/vim" name="android_external_vim" remote="omnirom" revision="android-9.0" />
   <project path="external/zip" name="android_external_zip"  remote="omnirom" revision="android-9.0" />

+  <!-- Incluede busybox -->
+  <project path="external/busybox" name="android_external_busybox"  remote="omnirom" revision="android-9.0" />
+
   <project path="packages/apps/Matlog" name="android_packages_apps_Matlog" remote="omnirom" revision="android-9.0" />
   <project path="packages/apps/MonthCalendarWidget" name="android_packages_apps_MonthCalendarWidget" remote="omnirom" revision="android-9.0" />
   <project path="packages/apps/OmniChange" name="android_packages_apps_OmniChange" remote="omnirom" revision="android-9.0" />
   ```

- To avoid build fail (in bootable/recovery):
```git
index e82495d..8bb680b 100644
--- a/fb2png/Android.mk
+++ b/fb2png/Android.mk
@@ -49,7 +49,7 @@ LOCAL_C_INCLUDES +=\
     external/libpng\
     external/zlib

-LOCAL_CFLAGS += -DANDROID
+LOCAL_CFLAGS += -DANDROID -Wno-sign-compare
 LOCAL_STATIC_LIBRARIES := libpng libz

 include $(BUILD_STATIC_LIBRARY)
 ```
 - To avoid build fail (in external/busybox/):
 ```git
 diff --git a/Android.mk b/Android.mk
index d74efad..8ba1f81 100644
--- a/Android.mk
+++ b/Android.mk
@@ -7,14 +7,12 @@ BB_PATH := $(LOCAL_PATH)
 BIONIC_ICS := false
 BIONIC_L := true

-BUSYBOX_WARNING_HIDE := -Wno-error=implicit-function-declaration -Wno-implicit-function-declaration -Wno-implicit-fallthrough \
-                       -Wno-sign-compare -Wno-format-overflow -Wno-shift-negative-value -Wno-logical-not-parentheses -Wno-return-type
-
+BUSYBOX_WARNING_HIDE := -Wno-error
 # Make a static library for regex.
 include $(CLEAR_VARS)
 LOCAL_SRC_FILES := android/regex/bb_regex.c
 LOCAL_C_INCLUDES := $(BB_PATH)/android/regex
-LOCAL_CFLAGS := -Wno-sign-compare
+LOCAL_CFLAGS := -Wno-sign-compare -Wno-unused-variable -Wno-unused-parameter
 LOCAL_MODULE := libclearsilverregex
 include $(BUILD_STATIC_LIBRARY)
```
