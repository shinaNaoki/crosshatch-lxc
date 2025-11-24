# Pixel-3-XL 内核编译与定制教程

## 1. 准备环境

- 系统要求：Ubuntu-22.04/Debian12 或其他 Linux 发行版（本文使用 Debian12）

- 配置前置：镜像源、普通用户、代理等

## 2. 安装必要软件包

```Bash

```

## 3. 准备内核及工具链（使用 repo 工具下载谷歌官方资源）

### 3.1 初始化配置

```Bash

```

### 3.2 安装 repo 工具

```Bash

```

将 `REPO_URL = 'https://gerrit.googlesource.com/git-repo'` 改为 `REPO_URL = 'https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'`

### 3.3 下载内核源码及工具链

```Bash

```

- 下载完成后，内核文件路径：`~/crosshatch/private/msm-google/`

- 可选操作：如需替换为 LineageOS 内核，先备份 `msm-google` 文件夹再替换

## 4. 内核定制修改

### 4.1 进入内核源码目录

```Bash

```

### 4.2 基础配置修改

```Bash

```

注释内容：`POST_DEFCONFIG_CMDS="check_defconfig && update_nocfi_config"`

### 4.3 驱动整合（确保开机声音、WIFI、触摸正常）

```Bash

```

将文件中所有 `m` 改为 `y`

```Bash

```

在倒数第二行添加：`source "drivers/staging/qcacld-3.0/Kconfig"`（LineageOS 内核需先注释 `config QCA_CLD_WLAN` 相关内容）

```Bash

```

末尾添加：`obj-$(CONFIG_QCA_CLD_WLAN)+= qcacld-3.0/`（LineageOS 内核通常已存在，无需修改）

```Bash

```

### 4.4 可选：添加 KernelSU 支持

```Bash

```

添加以下内容：

```Plain Text

```

### 4.5 应用 path_umount 补丁

```Bash

```

添加以下补丁内容：

```Diff

```

```Bash

```

### 4.6 添加 LXC/Docker 支持

```Bash

```

添加：`source "utils/Kconfig"`

```Bash

```

末尾添加：

```Plain Text

```

### 4.7 生成并调整配置文件

```Bash

```

配置菜单操作：

1. Networking support → Networking options → 开启 IP: tunneling

2. Device Drivers → Input device support → Touchscreens → 将 [M] 改为 [*]（LineageOS 内核无需操作）

3. Device Drivers → Staging drivers → 开启 Qualcomm Atheros CLD WLAN module

4. 按教程补充配置：[参考链接](https://mp.weixin.qq.com/s?__biz=MzU1NjYyNjA3MQ==&mid=2247485043&idx=1&sn=f0c7f008d27851ee0d88780711eebb21&chksm=fbc37f8bccb4f69d666182c9a22b97dc7776c62fc67303bb08dcf75944e2637649aa2ee66028&mpshare=1&scene=23&srcid=0706c2VKK7bFY5X50Qc7aQEU&sharer_sharetime=1688653779744&sharer_shareid=f3dd87dafbf3fa6c2772d66af5f1c719#rd)

5. Save 保存配置，长按 ESC 退出

### 4.8 保存配置文件

```Bash

```

## 5. 编译内核

```Bash

```

- 编译完成后，内核文件路径：`out/android-msm-pixel-4.9/dist/Image.lz4`

- 若报错，需先解决报错再重新编译

## 6. 打包 boot 镜像（基于 LineageOS-22.2）

### 6.1 准备文件

1. 下载 LineageOS 的 boot.img

2. 下载 Magisk 安装包，改后缀为 .zip 并解压

### 6.2 推送文件到手机

```Bash

```

- 若报错：开启开发者选项中的「允许 root 身份调试」，执行 `adb root`

### 6.3 打包镜像

```Bash

```

- 生成新镜像：`new-boot.img`（可先临时启动验证，再正式刷入）

## 7. 后续配置

### 7.1 KernelSU 说明

- 内核版本 4.9 仅支持 KernelSU v0.9.5，对应 APP 版本 1.0.1

- 推荐使用 Magisk（面具）替代

### 7.2 LXC 支持（推荐刷入模块）

- 下载模块：[LXC-5.0-Pro_Magisk-android24-R5.1.zip](https://github.com/tomxi1997/termux-packages/releases/download/v20/LXC-5.0-Pro_Magisk-android24-R5.1.zip)

- 刷入后通过 Termux 或 adb shell 运行 lxc 脚本即可使用

### 7.3 网络问题修复

- 启动 LXC 容器后 WiFi 异常：关闭 WiFi 等待片刻再重新打开

需要我帮你整理一份「关键步骤速查清单」，方便你编译时快速核对操作吗？
> （注：文档部分内容可能由 AI 生成）
