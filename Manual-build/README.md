# 手动编译Pixel 3XL内核（LineageOS维护的内核为例）

## 1. 准备环境

## 2. 下载内核以及准备工具

```Bash
# 克隆内核源码
git clone --depth 1 --branch lineage-22.2 https://github.com/LineageOS/android_kernel_google_msm-4.9.git crosshatch

~~# 准备mkdtimg工具（手动编译必需，缺失会导致报错）~~
~~git clone https://github.com/tomxi1997/android-mkdtimg-mkbootimg.git tools~~

# 准备lineageOS的预编译gcc（64位）
git clone https://github.com/LineageOS/android_prebuilts_gcc_linux-x86_aarch64_aarch64-linux-android-4.9 --depth 1 gcc64

# 准备lineageOS的预编译gcc（32位）
git clone https://github.com/LineageOS/android_prebuilts_gcc_linux-x86_arm_arm-linux-androideabi-4.9 --depth 1 gcc32

# 准备预编译clang工具（Google官方编译Pixel 3XL内核所用版本）
git clone --depth 1 https://github.com/LineageOS/android_prebuilts_clang_kernel_linux-x86_clang-r416183b.git clang
```

## 3. 准备环境、更改内核配置

```Bash
# 配置环境变量（可替换为自己的工具路径）
export PATH="/home/user/clang/bin:/home/user/gcc32/bin:/home/user/gcc64/bin:$PATH" 

# 进入内核源码目录
cd crosshatch
```

## 4. 开始编译

```Bash
# 加载默认配置
make CC=clang ARCH=arm64 SUBARCH=arm64 CROSS_COMPILE=aarch64-linux-android- CROSS_COMPILE_ARM32=arm-linux-androideabi- CLANG_TRIPLE=aarch64-linux-gnu- O=out b1c1_defconfig

# 禁用CFI和LTO，跳过dtbo生成（重要步骤）
./scripts/config --file ./out/.config -d LTO -d LTO_CLANG -d CFI -d CFI_PERMISSIVE -d CFI_CLANG -d BUILD_ARM64_APPENDED_DTB_IMAGE -d BUILD_ARM64_DT_OVERLAY -d BUILD_ARM64_APPENDED_DTB_IMAGE_NAMES -d BUILD_ARM64_DTC -d BUILD_ARM64_DTC_FLAGS

# 更新内核配置
make CC=clang ARCH=arm64 SUBARCH=arm64 CROSS_COMPILE=aarch64-linux-android- CROSS_COMPILE_ARM32=arm-linux-androideabi- CLANG_TRIPLE=aarch64-linux-gnu- O=out olddefconfig

# 开始编译
make CC=clang ARCH=arm64 SUBARCH=arm64 CROSS_COMPILE=aarch64-linux-android- CROSS_COMPILE_ARM32=arm-linux-androideabi- CLANG_TRIPLE=aarch64-linux-gnu- O=out -j$(nproc --all)
```

等待编译完成，内核输出路径：`out/arch/arm64/boot/Image.lz4`

## 5. 打包boot镜像

本节使用lineageOS-22.2版本的内核，请根据当前系统或内核选择对应的boot.img文件

### 前提准备

- 下载lineageOS的boot.img

- 参考kernelSU官网教程，以下为具体步骤：

#### a. 处理Magisk安装包

```Bash

```

#### b. 推送magiskboot工具到手机

```Bash
# 下载Magisk安装包，将后缀改为zip后解压
unzip Magisk-xxx.zip
```

#### c. 推送boot.img和编译好的内核

```Bash

```

#### d. 赋予文件可执行权限

```Bash

```

#### e. 解包boot.img

```Bash

```

#### f. 替换内核

```Bash

```

#### g. 重新打包并刷入

```Bash

```
