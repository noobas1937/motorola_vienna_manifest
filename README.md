# Motorola Edge 50 Neo (Vienna) kernel manifest
- Release tag: MMI-V1UI35H.11-39-16
- Android 15

## 1. Create folder & sync repo
<pre>mkdir -p ~/vienna_kernel && cd ~/vienna_kernel
repo init -u https://github.com/noobas1937/motorola_vienna_manifest -m default.xml
repo sync -j$(nproc --all)</pre>

## 2. Create symlinks
<pre>ln -s kernel_device_modules-6.1 kernel_device_modules-mainline
ln -s kernel_device_modules-6.1 kernel_device_modules</pre>

## 3. Copy vienna config
<pre>mkdir -p kernel_device_modules-6.1/kernel/configs/ext_config
cp -r kernel_device_modules-6.1/arch/arm64/configs/ext_config/moto-mgk_64_k61-vienna.config \
   kernel_device_modules-6.1/kernel/configs/ext_config/moto-mgk_64_k61-vienna.config</pre>

## 4. Build kernel
<pre>bazel build //kernel-6.1:kernel --//:kernel_version=6.1 --//:internal_config=true</pre>

## 5. Build kernel modules
<pre>export DEFCONFIG_OVERLAYS="ext_config/moto-mgk_64_k61-vienna.config"
bazel build //kernel_device_modules-6.1:mgk_64_k61.user</pre>

## 6. Build Motorola kernel modules
<pre>export DEFCONFIG_OVERLAYS="ext_config/moto-mgk_64_k61-vienna.config"
tools/bazel build   $(bazel query 'filter("mgk_64_k61.6.1.user$", //motorola/kernel/modules/...)')</pre>

## 7. Build MediaTek modules
<pre>bazel build \
$(bazel query 'kind(kernel_module, //vendor/mediatek/kernel_modules/...)' \
  | grep '\.mgk_64_k61\.6\.1\.user$') \
--//:kernel_version=6.1 \
--//:internal_config=true</pre>

## Clean sources
<pre>bazel clean --expunge </pre>
