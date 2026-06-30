# iOS Location Spoofer

用代理软件的 HTTPS 解密功能，把 Apple 地图定位骗到世界任何角落。

## 怎么回事

iPhone 看 Wi-Fi 信号和基站信号，拿着 BSSID 列表去问 Apple 这些设备在什么位置。Apple 回一份坐标清单，iOS 根据这些坐标算出自己在哪里。

这套配置做的事情很简单：**在 Apple 发回坐标的路上拦截下来，全部改成你想要的数字**。iPhone 拿到改造过的坐标，算出来就是你指定的地方。

不需要越狱，不需要额外装证书签发工具，代理软件自带 MITM 就够了。

## 支持哪些软件

| 软件 | 文件 | 导入方法 |
|------|------|---------|
| Shadowrocket（小火箭） | `.sgmodule` | 配置 → 模块 → 右上角 + |
| Surge | `.sgmodule` | 首页 → 模块 → 安装新模块 |
| Loon | `.lnplugin` | 设置 → 插件 → 添加插件 |
| Quantumult X | `.snippet` | 设置 → 重写 → 添加 |
| Stash | `.stoverride` | 覆写 → 安装覆写 |

## 怎么用

**第一步 — 开 HTTPS 解密**

软件里找到 MITM / HTTPS 解密开关，打开。

**第二步 — 装证书**

软件会生成一个 CA 证书。下载下来，去系统设置里：
1. 通用 → VPN 与设备管理 → 安装描述文件
2. 通用 → 关于本机 → 证书信任设置 → 打开这个证书的开关

这两步不做，HTTPS 流量解不开。

**第三步 — 导入配置**

点上面的导入链接，文件进去后记得勾上启用。

**第四步 — 验证**

断开重连 VPN。去设置里把定位服务关了再开。打开地图 App 看位置变了没——变了就对了。

## 改坐标

默认是 Apple Park。想换地方，在模块参数里改：

```
latitude=39.9042&longitude=116.4074
```

上面是北京的。

参数说明：

| 名字 | 默认值 | 干什么的 |
|------|--------|---------|
| `latitude` | 37.3349 | 目标纬度 |
| `longitude` | -122.00902 | 目标经度 |
| `horizontalAccuracy` | 39 | 定位精准度，越小看起来越准 |
| `verticalAccuracy` | 1000 | 垂直精度 |
| `altitude` | 530 | 海拔 |
| `failOpen` | true | 脚本出错就放行原始数据，不影响正常用 |
| `debug` | false | 打开后会打日志，没事别开 |

## 哪些域名会被拦截

- `gs-loc.apple.com` — Apple 全球定位
- `gs-loc-cn.apple.com` — Apple 中国定位
- `bluedot.is.autonavi.com` — 高德地图用的定位接口
- `bluedot.is.autonavi.com.gds.alibabadns.com` — 高德在阿里 DNS 上的 CDN

## 什么情况下不灵

- 纯 GPS 定位不受影响（开阔室外 GPS 直接连卫星，不走 Apple 服务器）
- App 自己接的高德百度 SDK 也不走这个接口
- 证书没信任一定不灵
- 室内或城市里效果最好，因为这时候 iOS 主要靠 Wi-Fi 定位

## 文件清单

```
Shadowrocket/
├── ios-location-spoofer.sgmodule    # Surge、小火箭
├── ios-location-spoofer.lnplugin    # Loon
├── ios-location-spoofer.snippet     # Quantumult X
├── ios-location-spoofer.stoverride  # Stash
├── location-spoofer.js              # 核心脚本（四个软件共用）
├── location-spoofer-qx.js           # QX 专用（base64 编解码差异）
└── location-spoofer-config.json     # 坐标配置样板
```
