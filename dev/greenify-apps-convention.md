# Android 绿色应用公约（草案）

## 宗旨

这是一项旨在推动Android生态中的优秀应用共同维护一个更加良性的『设备体验』而发起的开放公约。

**设备体验**：影响效应超出用户与应用进行显性交互的过程之外，在用户感知中属于设备整体性的体验因素的总称。包括设备的安全性、整体流畅性、耗电程度、发热程度等。

由于Android系统的设备体验是由设备本身的软硬件及安装在设备中的众多应用所共同影响的，后者的影响往往随着安装的应用数量增长而迅速扩大。这种由应用所造成的外溢性影响，存在着典型的[『公地悲剧』](http://baike.baidu.com/item/%E5%85%AC%E5%9C%B0%E6%82%B2%E5%89%A7)。安装的众多应用中，某一个应用对于设备体验的损害往往很难被用户直接辨识，以至设备体验问题长期得不到应用开发团队的足够重视。造成的后果间接的由全部应用，乃至整个Android生态共同承担。

因此，除了加强用户对于设备体验损害的辨识能力外，有必要推动整个Android开发社区以更高的标准优化各自应用的设备体验影响，共同维护一个良性的Android生态。

## 核心原则

此公约的核心原则完全遵照Android本身的演进方向（包括[Android O所引入的新变化](https://developer.android.google.cn/preview/behavior-changes.html)），在确保应用核心功能不受影响的前提下，减少不必要的应用后台行为，并以更加高效、节能的调度机制改善后台行为的调度。

## 规约

### 必要部分

1. Target SDK Version >= 24 (Android 7.0)

   [Project Svelte在Android N中得到了一些关键的的强化](https://developer.android.google.cn/about/versions/nougat/android-7.0-changes.html#bg-opt)，有助于降低应用后台行为对设备体验的影响。

2. 不在运行时强制请求 **『读取手机状态和身份（READ_PHONE_STATE）』** 权限。

   若应用中的某些功能（如通话相关的特性）依赖此权限（须具备逻辑上的合理性），则只能在对应功能交互中请求此权限。即便用户拒绝授予权限，不依赖此权限的功能仍须保持可用。

   原因：IMEI泄露是目前用户隐私和手机安全中的一个重灾区。它具有相当的隐蔽性，在Android 6.0之后的运行期权限体系中依然未能获得清晰的信息披露。由于Android系统仅仅将其显示为『读取手机状态和身份』，使得大部分用户在应用请求此项权限时虽然困惑，但仍未意识到授予这个权限背后存在的安全隐患。

3. 除用户的主动交互触发外，避免启动其它应用未处在运行中的进程。

   原因：非主动交互触发的进程启动，即通常所说的『交叉唤醒』。由于交叉唤醒在应用之间往往具有连锁效应，在安装有较多关联应用（例如集成了相同SDK的多个应用）的情况下极易触发『链式唤醒』，造成设备流畅性的急剧下降、耗电上升（及内存急剧消耗），带来严重的设备体验损害。

4. 使用请求唤醒CPU的周期性Alarm、JobScheduler的周期最小不低于30分钟，建议不低于1小时。

5. 为用户提供可达成『后台纯净』目标的选项。（不必默认开启）

   后台纯净：指符合[面向Android O的应用开发要求](https://developer.android.google.cn/preview/behavior-changes.html#o-apps)中关于后台运行的约束。

6. 不在AndroidManifest.xml中静态声明针对以下广播的接收器：

* Intent.ACTION_USER_PRESENT
* Intent.ACTION_POWER_CONNECTED
* Intent.ACTION_POWER_DISCONNECTED
* Intent.ACTION_MEDIA_SCANNER_STARTED
* Intent.ACTION_MEDIA_SCANNER_FINISHED
* WifiManager.SCAN_RESULTS_AVAILABLE_ACTION

### 建议部分

1. 避免使用『访问外部存储』权限，以Context.getExternalCacheDir()、Context.getExternalFilesDir()等方式替代。
