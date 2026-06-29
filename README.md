# ImmortalWrt R4S 1G 自动编译

为 NanoPi R4S（1G 内存版本）自动编译 **ImmortalWrt 稳定版**固件，每天自动检查上游更新，有新版本才编译并发布 Release。

## ✨ 功能特点

- **稳定版而非开发版**：锁定 `openwrt-25.12` 分支，不用每周吃上游开发版的不稳定 bug
- **自动检查更新**：每天检查一次源码，没有新提交就跳过编译，不浪费 Actions 额度
- **自动编译 + 自动发布**：检测到更新后全自动跑完编译、打包、发布到 Release，无需人工干预
- **预装插件**：`dockerman`（Docker 管理）+ `passwall` + `homeproxy`（科学上网）+ `upnp`
- **网络优化**：Flow Offloading、Full Cone NAT、BBR 拥塞控制、irqbalance 中断均衡
- **中文界面**：预装中文语言包
- **空间预留**：RootFS 给到 1536MB，给三大插件留足安装空间

## 🚀 使用方法

### 1. Fork 本仓库

点击右上角 Fork，把仓库复制到你自己的账号下。

### 2. 启用 Actions

进入你 Fork 后仓库的 **Actions** 标签页，点击启用。

### 3. 等待自动编译，或手动触发

- **自动**：每天 UTC 18:00（北京时间凌晨 2:00）会自动检查更新，有新版本才编译
- **手动**：进入 Actions → 选择 workflow → `Run workflow`，可以勾选 "强制编译" 跳过更新检查，立即编译当前最新源码

### 4. 下载固件

编译成功后，去仓库的 **Releases** 页面下载：

| 文件 | 用途 |
|---|---|
| `*-squashfs-sysupgrade.img.gz` | 刷机/升级用，**最常用** |
| `*sha256sums*` | 校验文件完整性 |
| `*.manifest` | 固件内置软件包清单 |

### 5. 刷写固件

- **全新刷写**（TF 卡）：用 [balenaEtcher](https://www.balena.io/etcher/) 把 `img.gz` 直接写入卡
- **在线升级**（已有 OpenWrt/ImmortalWrt 系统）：LuCI → 系统 → 备份升级 → 上传同一个 `img.gz` 文件，自行决定是否保留配置

## ⚙️ 常用自定义项

打开 `.github/workflows/build.yml`，找这几处改：

```yaml
env:
  REPO_BRANCH: openwrt-25.12   # 上游出新稳定版分支时改这里
```

```yaml
CONFIG_TARGET_ROOTFS_PARTSIZE=1536   # TF 卡较小（如8G）可以调回 1024
```

```yaml
CONFIG_PACKAGE_luci-app-dockerman=y   # 不需要的插件可以删掉对应行
CONFIG_PACKAGE_luci-app-passwall=y
CONFIG_PACKAGE_luci-app-homeproxy=y
```

```yaml
schedule:
  - cron: '0 18 * * *'   # 改检查频率，默认每天一次
```

## 📜 致谢

固件基于 [ImmortalWrt](https://github.com/immortalwrt/immortalwrt) 官方源码编译，部分网络优化思路参考自 R2S/R4S 社区（天灵 / GC 等）长期沿用的常见配置。
