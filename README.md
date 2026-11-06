# easytier-min-android
EasyTier 的最小原生 Android 客户端：不依赖 WebView、不依赖任何第三方库，界面用 framework View 手写，底层直接调用 EasyTier 官方的 JNI 层。

    minSdk 26（Android 8.0）
    仅 arm64-v8a
    单实例：一个网络名 + 密钥 + 若干对端
    实时显示同网络其它设备的 IP、主机名、直连/中继、延迟、隧道类型
    产物：一个 APK，dex < 150 KB，体积几乎全部来自 libeasytier_android_jni.so
# 已知限制

    协议裁剪：去掉了 quic / kcp / wireguard / upnp / cli。连使用 quic 或 kcp 的节点会失败；配置里也不要写 quic:// / kcp:// 的 listener 或 peer。要全功能，改 scripts/patch-easytier-features.sh 里的 FEATURES 列表。
    仅 arm64-v8a：32 位设备装不上。要覆盖需在 app/build.gradle.kts 加 armeabi-v7a，并在 CI 里加 -t armeabi-v7a。
    单实例：不支持同时连多个网络。多实例要改成 retainNetworkInstance 的多实例模型。
    无开机自启：Android 8 上开机拉起 VpnService 需要额外处理。
    端口固定 11010（tcp+udp），与本机其它 EasyTier 实例冲突时会启动失败。

# 排障

    界面不显示日志，排障用 adb logcat -s EtVpnService EasyTier-JNI。
    设备列表为空说明还没拿到路由：检查网络名称/密钥是否一致、对端是否可达。
    连不上先看：网络名称/密钥是否一致、对端是否可达、是否被裁剪的协议。
