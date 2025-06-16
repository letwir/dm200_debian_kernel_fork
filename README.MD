# dm200_debian_kernel
:SDカードを高速化したいフォーク

提供されたソースは、このリポジトリの初期コミット。  
defconfigは `onyx2_warp_defconfig`

## 主な変更点  
* FrameBufferの初期化処理変更
* キーリピート有効化
* suspend時に各種デバイスの電源が切れるように処理を強制的に変更
* USB-HOST時にVBUS出力しないように変更
* キーボードのキー定義変更
* Framebufferの色深度を32bitで初期化
* カーネルコンフィグの変更多数
* /dev/uinputを有効化

## コンパイル  
Ubuntu14.04環境で (16.04ではaptで入るgccが新しすぎるため、14.04を使うほうが楽)  
```
apt-get -y install qemu-debootstrap
#ビルド用のディレクトリを作ってビルド用のUbuntuをダウンロード
REPO=dm200_debian_kernel_fork
BUILDDIR=/qemu/Ubuntu14
cd $BUILDDIR
git clone ここ
sudo debootstrap \
  --arch armhf \
  trusty \
  $BUILDDIR \
  http://ports.ubuntu.com/ubuntu-ports 
sudo chroot $BUILDDIR

#以下chrootして内部で実行
apt-get -y install libacl1-dev libaio-dev libattr1-dev libcap-ng-dev libcurl4-gnutls-dev libepoxy-dev libfdt-dev libgbm-dev libglusterfs-dev libgnutls28-dev libiscsi-dev libjpeg-dev libnuma-dev libpci-dev libpixman-1-dev libproxmox-backup-qemu0-dev libsdl1.2-dev libseccomp-dev libslirp-dev libspice-protocol-dev libspice-server-dev libsystemd-dev liburing-dev libusb-1.0-0-dev libusbredirparser-dev libvirglrenderer-dev libzstd-dev python3-sphinx python3-sphinx-rtd-theme python3-venv quilt uuid-dev xfslibs-dev
apt-get -y install linux-source build-essential libssl-dev xz-utils libssl-dev bc  
apt-get -y install fakeroot libncurses5-dev  
apt-get -y install gcc-arm-linux-gnueabihf  
apt-get -y install lzop  
export ARCH=arm  
export CROSS_COMPILE=arm-linux-gnueabihf-  
cd $REPO
make defconfig onyx2_warp_defconfig dm200_defconfig
make dep  
make zImage -j32  
exit
#外からmkkrnlimg実行
$BUILDDIR/$REPO/mkkrnlimg $BUILDDIR/$REPO/arch/arm/zImage kernel.img  
```

rootfs作るなら
```

 sudo qemu-debootstrap --arch=armhf --variant=buildd bookworm debian-dev http://ftp.jp.debian.org/debian/
```

##以下からソースを頂戴した  
https://github.com/TinkerBoard/android-6.0.1_kernel  
https://github.com/armbian/firmware/tree/master/ap6210
https://github.com/RK3128-CFW/rk3128-cfw.github.io  
https://github.com/aosp-rockchip/README/blob/23c1487f8060c79f5ab73ebc7bccce10c6a8b655/patches-kernel/0001-support-ddr-change-freq.patch#L6  
  
##参考  
https://qiita.com/progrunner/items/d2ab0a85b3881a4b7ed8  
https://askubuntu.com/questions/1111914/build-dependencies-with-apt-get-build-dep-for-linux-image-4-14  
