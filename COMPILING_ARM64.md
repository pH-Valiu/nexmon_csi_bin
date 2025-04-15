# Making your own compiled binaries for nexmon_csi on ARM64

## Supported kernels
Supported kernels according to nexmon_csi:

Kernel | 
-------|
4.19   |
5.4    |
5.10   |

Supported kernels for base nexmon patches, therefore it might also be possible to compile nexmon_csi for those version:

Kernel | Link
-------|-----
4.9    |
4.14   |
4.19   |
5.4    |
5.10   | https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2022-01-28/
5.15   |

In general, whenever you want to start compiling, I recommend starting with a fresh new rpi image.
Images can be found under this link:
`https://downloads.raspberrypi.org/raspios_lite_arm64/images/`

## Prerequesites
After you have flashed the rpi with the image, add `enable_uart=1` to the `config.txt` 
file in the boot partition to enable a stable connection over a UART&lt;-&gt;USB connector.
Otherwise, you will not be able to access the rpi after installing the patched wifi module,
unless you are connected via ssh over ethernet.
Therefore, connect to rpi via:
- UART
- SSH over Ethernet

Next, call:
```sh
sudo su
apt-get update
```

Do NOT call `apt-get upgrade` otherwise the kernel might update and you will not be able to compile the binaries for your wished version!

### Installing libs for compilation
Run:
```sh
apt-get -y install git libgmp3-dev gawk qpdf bison flex make autoconf libtool texinfo
```
These libraries are requirements as specified in the nexmon README.
During my testing, I found the following libraries to be requirements as well:
```sh
apt-get libssl-dev bc xxd python2 libncurses5-dev
```

### Installing Kernel Headers
Installing the kernel headers via `apt-get install raspberrypi-kernel-headers` might work for you,
for me it didn't. Therefore, we need to manually add them. 
This can be achieved in two different ways.
Either:
- Manual Download: download the correct headers manually and create a sym-link to /lib/modules/KERNEL_VERSION/build
- Automatic Download: use rpi-source
<br> For me the manual download didn't work, but the automatic did.
Others had different experiences.

#### Manual Download
Navigate to `https://archive.raspberrypi.org/debian/pool/main/r/raspberrypi-firmware/`
and download the correct raspberrypi-kernel-headers for your kernel version for arm64.
Call:
```sh
DOWNLOAD_LINK = HERE_YOUR_WEB_LINK_TO_KERNEL_HEADERS

cd /usr/src
wget -O linux-headers.deb $(DOWNLOAD_LINK)
dpkg -i linux-headers.deb
```

Check if the sym-link has been automatically created:
```sh
ls -lah /lib/modules/$(uname -r) | grep build
```
Otherwise create it yourself:
```sh
ln -s /usr/src/linux-headers /lib/modules/$(uname -r)/build
```


#### Automatic Download
Call:
```sh
wget https://raw.githubusercontent.com/RPi-Distro/rpi-source/master/rpi-source -O /usr/local/bin/rpi-source 
chmod +x /usr/local/bin/rpi-source 
/usr/local/bin/rpi-source -q --tag-update

rpi-source
```

This command might fail. If it fails during the step of `make modules_prepare`, use the following commands:
```sh
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2711_defconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules_prepare
```
If prompted with a terminal config menu set all values to default.
The script might try to compile the whole kernel. This should not be necessary, I think.
The reason we include the kernel source files is to have the compilation pipeline ready to later compile
the `brcmfmac.ko` driver.

Now, check if the sym-link has already been automatically created:
```sh
ls -lah /lib/modules/$(uname -r) | grep build
```
Otherwise create it yourself, after specfying the path:
```sh
ln -s PATH_TO_HEADERS_DOWNLOADED_VIA_RPI_SOURCE /lib/modules/$(uname -r)/build
```

### Libraries and Modifications due to ARM64
Call the following commands to establish an internal 32-bit toolchain:
```sh
dpkg --add-architecture armhf
apt-get install libc6:armhf libisl23:armhf libmpfr6:armhf libmpc3:armhf libstdc++6:armhf
```

Check which versions of `libisl` and `libmpfr` are on your system:
```sh
ls /usr/lib/arm-linux-gnueabihf | grep libisl
ls /usr/lib/arm-linux-gnueabihf | grep libmpfr
```
Execute the following commands, modifying the versions of the libraries if needed:
```sh
ln -s /usr/lib/arm-linux-gnueabihf/libisl.so.23.0.0  /usr/lib/arm-linux-gnueabihf/libisl.so.10
ln -s /usr/lib/arm-linux-gnueabihf/libmpfr.so.6.1.0 /usr/lib/arm-linux-gnueabihf/libmpfr.so.4
```

### nexmon_csi compilation
Let's clone the repo:
`git clone https://github.com/seemoo-lab/nexmon.git`
Let's also directly clone the nexmon_csi repo:
```sh
cd nexmon/patches/bcm43455c0/7_45_189/

git clone https://github.com/seemoo-lab/nexmon_csi.git
```

Now you can either manually make all the following changes and then call the `make` command in the nexmon_csi directory.
Or, you call the provided shell commands which can be found at the bottom. 
That script might fail though, when nexmon and nexmon_csi get updated, so doing it manually and checking whether the changes are even required is the safer way.

- Navigate into it: `cd nexmon`
- Open `nexmon/buildtools/b43-v3/debug/b43-beautifier` and change:
	- `#!/usr/bin/env python` to `#!/usr/bin/env python2`
- Move back: `cd nexmon`
- Open `setup_env.sh` and change:
	- `ARCH=arm` to `ARCH=arm64`
	- `SUBARCH=arm` to `SUBARCH=arm64`
	- `KERNEL=kernel7` to `KERNEL=kernel8`
- Open `firmwares/bcm43455c0/7_45_189/definitions.mk` and change:
	- `NEXMON_ARCH=armv7l-r` to `NEXMON_ARCH=armv8-a`
- Move back: `cd nexmon`
- Setup the build environment: `source setup_env.sh`
- Compile some build tools and extract the ucode and flashpatches from the original firmware files: `make`
- Move to: `cd patches/bcm43455c0/7_45_189`
- Clone nexmon_csi repo: `git clone https://github.com/seemoo-lab/nexmon_csi.git`
- Open `nexmon_csi/Makefile` and change:
	- at line _102_: add `aarch64` to the clause checking for valid `uname -m` versions
	- at line _111_: remove the '#' to have a log in case sth goes bad
	- at line _323_: add `aarch64` to the clause checking for valid `uname -m`versions
- Depending on which kernel version you are, you will either use one of the existing driver packages in `nexmon_csi` directory, or you will have to copy the specific driver package from `nexmon/patches/driver` into `nexmon_csi`. If you choose to use one from `nexmon/patches/driver`, you will have to modify `nexmon_csi/Makefile` further (Should be a straightforward task). Either way, you will have a driver directory you want to use.
- Move into it. e.g. `cd brcmfmac_5.10.y-nexmon` (or your kernel version specific folder)
- Open `Makefile` and add `dmi.o` to the `brcmfmac-objs list`, if not already there. If `dmi.c` does not exist in your drivers directory, then don't add this line.
- Make nexmon_csi: `make ARCH=arm64 install-firmware`

Alternative immediate shell commands:
```sh

```
Now call make inside the nexmon_csi directory:
```sh
make ARCH=arm64 install-firmware
```

### makecsiparams compilation
Navigate to `nexmon_csi/utils/makecsiparams`.
Before we compile, we have to modify the Makefile to not dynamically link but rather statically link
the required libraries by adding `-static` in the compliation step for `makecsiparams`.
Then call `make` and (if wished) `make install`


### nexutil compilation
Navigate to `nexmon/utilities/nexutil` and call `make` and (if wished) `make install`

## Collecting all files at one place for future export
We need:
- `makecsiparams`: --> `nexmon_csi/utils/makecsipararms/makecsiparams`
- `nexutil`: --> `nexmon/utilities/nexutil/nexutil`
- `brcmfmac43455-sdio.bin`: --> `nexmon_csi/brcmfmac43455-sdio.bin`
- `brcmfmac.ko`: --> `nexmon_csi/brcmfmac_YOUR_VERSION.y-nexmon/brcmfmac.ko`

