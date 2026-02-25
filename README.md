# Android_3711_Activity

## 一、原理

在 AndroidManifest 里注册超过 3711 个 Activity，会让系统设置（`com.android.Settings`）在加载应用信息时触发 Binder 事务超限崩溃，导致卸载界面打不开、无法卸载。

## 二、技术细节

触发阈值：Activity 数量 > 3711 时触发

崩溃点：系统设置调用 `getPackageInfoAsUser` → 内部 `hasPictureInPictureActivities` 方法

崩溃原因：

Binder 单次事务数据上限约 1MB

几千个 Activity 元数据超出上限 → `FAILED_BINDER_TRANSACTION`

旧版系统未捕获异常 → Settings 直接闪退

结果：用户在设置里点开该应用信息 → 设置崩溃 → 无法卸载

## 三、如何实现

在 `AndroidManifest.xml` 中批量注册大量空 Activity：

```xml
<!-- 重复注册 4000+ 个空 Activity -->
<activity android:name=".EmptyActivity1" />
<activity android:name=".EmptyActivity2" />
<activity android:name=".EmptyActivity3" />
<!-- ... 一直到 4000+ -->
```

配合脚本批量生成即可。

> [!CAUTION]
> 
> **仅技术演示，请勿滥用**

> [!IMPORTANT]
>
> 在 Android 12L 及以上已修复
