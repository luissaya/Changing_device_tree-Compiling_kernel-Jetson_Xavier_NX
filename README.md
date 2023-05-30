# Changing device tree and compiling Kernel for Nvidia Jetson Xavier NX
[reference](https://medium.com/@haoye94/editing-device-tree-and-compiling-kernel-for-nvidia-jetson-xavier-nx-11a1df20939c)

* Go to `/home/USER/projects` and create a folder to store all related files for the project.
  ```BASH
  cd $HOME/projects
  mkdir changing-device-tree-compiling-kernel
  ```

## Compiler
On the host machine which is most likely an x86 architecture, we need to download the corresponding compiler and set it up for cross-compilation.  
NVIDIA recommends using the `Linaro 7.3.1 2018.05` toolchain for this. Download the pre-built toolchain binaries [here](http://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz), follow the next steps:[Reference](https://developer.ridgerun.com/wiki/index.php?title=Jetson_Nano/Development/Building_the_Kernel_from_Source)
* Go to `changing-device-tree-compiling-kernel` and download the compiler.
  ```BASH
  cd changing-device-tree-compiling-kernel
  wget http://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz
  ```
* Create the folder `l4t-gcc/` and unzip the compiler inside
  ```BASH
  mkdir l4t-gcc
  tar -xvf gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz -C l4t-gcc/
  ```
## NVIDIA files
Go to [Jetson Linux Archive](https://developer.nvidia.com/embedded/jetson-linux-archive) and find the proper version, then click to go to the downloaded page. There, download `Linux for Tegra BSP`, `BSP sources` and `sample root file system (RFS)`.
![files](./img/files.png)
*Note: Download the tree files inside the default folder `Downloads/`*
* Extract Linux for Tegra from `Jetson_Linux_R35.1.0_aarch64.tbz2`
  ```BASH
  tar -xvf ../../Downloads/Jetson_Linux_R35.1.0_aarch64.tbz2
  ```
* Extract the `Sample_Root_Filesystem` inside the folder `Linux_for_Tegra/rootfs/` 
  ```BASH
  sudo tar -xvpf ../../Downloads/Tegra_Linux_Sample-Root-Filesystem_R35.1.0_aarch64.tbz2 -C Linux_for_Tegra/rootfs/
  ```
* Extract the `Sample_Root_Filesystem` inside the folder `Linux_for_Tegra/rootfs/` 
  ```BASH
  sudo tar -xvpf ../../Downloads/Tegra_Linux_Sample-Root-Filesystem_R35.1.0_aarch64.tbz2 -C Linux_for_Tegra/rootfs/
  ```
In the *public_sources.tbz2* (BSP sources zip file), there will be many other zip files inside, but we are only interested in `kernel_src.tbz2`. Extract this file into a folder to named “source”
* Create the folder `source/`
  ```BASH
  mkdir source
  ```
* Extract the `kernel_src.tbz2` from *public_sources.tbz2*.
  ```BASH
  sudo tar -xvpf ../../Downloads/public_sources.tbz2 kernel_src.tbz2 -C source/
  ```
Create `extlinux.conf`
*  create a directory called `extlinux/` and create `extlinux.conf`
  ```BASH
  mkdir Linux_for_Tegra/rootfs/boot/extlinux/
  touch Linux_for_Tegra/rootfs/boot/extlinux/extlinux.conf
  ```
* Open `extlinux.conf` and copy the next content.
  ```BASH
  sudo vim touch Linux_for_Tegra/rootfs/boot/extlinux/extlinux.conf 
  ```
  content:   
  ```BASH
  TIMEOUT 30
  DEFAULT primary
  MENU TITLE L4T boot options
  LABEL primary
  MENU LABEL primary kernel
  LINUX /boot/Image
  INITRD /boot/initrd
  APPEND ${cbootargs} root=/dev/mmcblk1p1 rw rootwait rootfstype=ext4 console=ttyS0,115200n8 console=tty0 fbcon=map:0 net.ifnames=0
  ```
## Change Pinmux
If the pin functions defer from dev kit, changes to pinmux are most likely required. This step can only be done on windows since it makes use of Excel Macros

Download [pinmux excel file](./docs/Jetson_Xavier_NX_Pinmux_Configuration_Template_v1.06%20.xlsm).
Editing the excel file is pretty straightforward, edit the orange area.
![excel-edit](./img/pinmux-1.png)
Once done with your changes, click on the “Generate DT File” button, give your board a name. and you should see 3 files generated in the same folder.
![excel-edit2](./img/pinmux-2.png)
There should be 3 files generated in the same folder. Copy these files to your Ubuntu machine in the folder `changing-device-tree-compiling-kernel`. 
![excel-edit3](./img/pinmux-3.png)
## Generate .cfg files

## Build kernel and device tree

## Replace .dtb files

## Flash device tree