# Aplicando patches no openwrt
```
# sudo apt install build-essential clang flex bison g++ gawk gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev python3-distutils rsync unzip zlib1g-dev file wget
# sudo pacman -S --needed base-devel autoconf automake bash binutils bison bzip2 fakeroot file findutils flex gawk gcc gettext git grep groff gzip libelf libtool libxslt m4 make ncurses openssl patch pkgconf python rsync sed texinfo time unzip util-linux wget which zlib

git clone https://git.openwrt.org/openwrt/openwrt.git
cd openwrt
git pull
#git checkout v23.05.3

./scripts/feeds update -a
./scripts/feeds install -a

wget https://downloads.openwrt.org/releases/23.05.3/targets/mediatek/mt7622/config.buildinfo -O .config
make menuconfig

make -j8 target/linux/{clean,prepare} V=s QUILT=1
cd build_dir/target-*/linux-*/linux-*
wget https://raw.githubusercontent.com/ramonalvesmodesto2/linux/master/patches/0014-add-a-sysctl-to-enable-disable-tcp_collapse-logic.patch -O  patches/generic/689-add-a-sysctl-to-enable-disable-tcp_collapse-logic.patch
wget https://raw.githubusercontent.com/ramonalvesmodesto2/linux/master/patches/0001-audit-check-syscall-bitmap-on-entry-to-avoid-extra-w.patch -O patches/generic/690-audit-check-syscall-bitmap-on-entry-to-avoid-extra-w.patch
#wget https://raw.githubusercontent.com/ramonalvesmodesto2/linux/master/patches/0020-Add-a-sysctl-to-allow-TCP-window-shrinking-in-order-.patch -O patches/generic/691-Add-a-sysctl-to-allow-TCP-window-shrinking-in-order-.patch
wget https://raw.githubusercontent.com/ramonalvesmodesto2/linux-network-tweaking/main/699-tcp-hybla-max.patch -O patches/generic/699-tcp-hybla-max.patch
quilt new patches/generic/689-add-a-sysctl-to-enable-disable-tcp_collapse-logic.patch
quilt new patches/generic/690-audit-check-syscall-bitmap-on-entry-to-avoid-extra-w.patch
#quilt new patches/generic/691-Add-a-sysctl-to-allow-TCP-window-shrinking-in-order-.patch # Não necessário no openwrt com kernel 6
quilt new patches/generic/699-tcp-hybla-max.patch
cd ../../../../ 
make target/linux/update
make -j8 target/linux/{clean,prepare} V=s QUILT=1
ionice -c 3 chrt --idle 0 nice -n19 make -j8

```

# Cloudflare Linux Kernel Patches

This repository contains some Linux Kernel patches, which where not submitted to the mainline kernel tree. These patches may be not generic enough for inclusion into the mainline kernel tree, declined by the kernel maintainers etc.

## Supported Kernel Versions

Normally the patches in this repository should be applicable on top of the latest [Linux Kernel long-term support ("LTS") release](https://www.kernel.org/releases.html).  Other versions are not considered.

These patches are provided as-is without any support guarantee, but contributions are welcome.
