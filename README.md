# How to cross-compiling on Raspberry Pi
## 1. System requirements
I test the code with Ubuntu 16.04(64-bit) system. The workspace setting is as following:
```
cd ~
mkdir workspace_raspberry_pi
cd workspace_raspberry_pi
git clone https://github.com/qinghuizeng/raspberrypi.git
```
If your workspace setting is different with it, please update the corresponing path occuring in the following documents. 

## 2. Intall toolchain
This repository includes a copy of cross-compiling toolchain for Raspberry Pi from Raspberry Pi official github repository, the commit id is 49719d5544cd33b8c146235e1420f68cd92420fe. It's under 'tools' folder. 

Raspberry Pi Foundation supplies the toolchain officially in github. You can also download it seperately as following:
```
git clone https://github.com/raspberrypi/tools
```

## 3. Toolchain selection
There have several toolchains avaiable, because I am using Ubutun 16.04(64-bit) system, I select the following one:
```
gcc-linaro-arm-linux-gnueabihf-raspbian-x64/
```
About the toolchain selection, please refer Raspberry official document on the [kernel cross-compiling](https://www.raspberrypi.org/documentation/linux/kernel/building.md)

## 4. Rootfs 
To do cross-compiling, except the GCC toolchain, you also need rootfs which supplies all kinds of libraries and header files. The toolchain we select doesn't supply one complete rootfs. So we need copy the entire `/lib` and `/usr` folder from the target platform and build the available rootfs.
```
cd ~/workspace_raspberry_pi
mkdir rootfs
cd rootfs
rsync -rl --delete-after --safe-links pi@10.167.24.35:/{lib,usr} ./
```

Please replace 10.167.24.35 by the IP of your Raspberry Pi.

About more detail please refer the [wiki](https://github.com/raspberrypi/tools/wiki) of raspberrypi tools.

## 5. CMAKE_TOOLCHAIN_FILE setting
I use cmake for cross-compiling and the CMAKE_TOOLCHAIN_FILE is `pi.gcc.cmake`. Please edit this file if you working folder, toolchain or rootfs folder is different with me. 

## 6. Build and the sample code
`cmake-hello-world` is a copy of the [sample code](https://github.com/jameskbride/cmake-hello-world.git ) from raspberrypi wiki. Use the following command to build the code:
```
cd ~/workspace_raspberry_pi/cmake-hello-world
mkdir build
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=$HOME/workspace_raspberry_pi/pi.gcc.cmake .. 
make
```

Use the following to copy the code to target platform and run it:
```
scp CMakeHelloWorld pi@110.167.24.35:/home/pi/
ssh pi@10.167.24.35 ./CMakeHelloWorld
```

## 7. Reference
- [Wiki of RaspberryPi tools](https://github.com/raspberrypi/tools/wiki)
