```
⢀⣠⣾⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⠀⠀⠀⣠⣤⣶⣶
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⠀⠀⢰⣿⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣧⣀⣀⣾⣿⣿⣿⣿
⣿⣿⣿⣿⣿⡏⠉⠛⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⣿
⣿⣿⣿⣿⣿⣿⠀⠀⠀⠈⠛⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠿⠛⠉⠁⠀⣿
⣿⣿⣿⣿⣿⣿⣧⡀⠀⠀⠀⠀⠙⠿⠿⠿⠻⠿⠿⠟⠿⠛⠉⠀⠀⠀⠀⠀⣸⣿
⣿⣿⣿⣿⣿⣿⣿⣷⣄⠀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⠏⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠠⣴⣿⣿⣿⣿                Nexmonster / Nexmon_CSI bin ⚡
⣿⣿⣿⣿⣿⣿⣿⣿⡟⠀⠀⢰⣹⡆⠀⠀⠀⠀⠀⠀⣭⣷⠀⠀⠀⠸⣿⣿⣿⣿                ======================= ===  
⣿⣿⣿⣿⣿⣿⣿⣿⠃⠀⠀⠈⠉⠀⠀⠤⠄⠀⠀⠀⠉⠁⠀⠀⠀⠀⢿⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⢾⣿⣷⠀⠀⠀⠀⡠⠤⢄⠀⠀⠀⠠⣿⣿⣷⠀⢸⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⡀⠉⠀⠀⠀⠀⠀⢄⠀⢀⠀⠀⠀⠀⠉⠉⠁⠀⠀⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣧⠀⠀⠀⠀⠀⠀⠀⠈⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢹⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⠃⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢸⣿⣿
```

## Nexmonster / nexmon_csi_bin

Get started with CSI collection in **seconds**! ⚡

For Raspberry Pi 3B+ and 4.

## Install

**Important**: Following kernel versions are not true Linux Kernel versions, rather they are Raspberrypi-Linux Kernel versions containing the rpi's suffixes (like -v8+, or -v7l)

Use one of the RaspberryPi OS versions for 32 bit:

Kernel Version | Link
---------------|-----
5.10.92        | https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2022-01-28/
5.4.51         | https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2020-08-24/
4.19.97        | https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2020-02-14/


For 64 bit, use this version:

Kernel Version | Link
---------------|-----
5.10.92	       | https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2022-01-28/

Run the [install script](install.sh) on Raspberry Pi:

```bash
curl -fsSL https://raw.githubusercontent.com/nexmonster/nexmon_csi_bin/main/install.sh | sudo bash
```

You have installed Nexmon_CSI. 
Now refer to the [Usage section of Nexmon_CSI](https://github.com/nexmonster/nexmon_csi/tree/pi-5.10.92#usage).

## Help
If you need with help with Nexmon_csi, create and Issue or Discussion in the Nexmon_csi repository and tag me ([@zeroby0](https://github.com/zeroby0)).

If you find a bug in the install script or the binairies, please create an Issue or a Discussion in this repository.

### Notes
- Note #1
Pre-compiled binaries are not available for all versions
of the kernel. The install script will alert you if they are not
available. Please [compile nexmon_csi from source](https://github.com/seemoo-lab/nexmon_csi#bcm43455c0)
in that case.

- Note #2
[base](base) folder contains a precompiled version for a TRUE 64bit 5.10.92 kernel (without suffixes) as well, but it was never properly tested.
Use at own risk and test it yourself.


