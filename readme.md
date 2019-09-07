

## Windows下编译ffmpeg



## 前提条件：

- 网络畅通良好

- 安装VS 我使用的是`cn_visual_studio_community_2015_x86_dvd_6847368`

- 安装cuda 我装的是`cuda_10.1.243_426.00_win10`

- 安装msys64 我装的是`msys2-x86_64-20190524`

- 备好ffmpeg源码` FFmpeg-release-4.2`

- nv_sdk文件  看第三点 https://blog.csdn.net/cwbguaijie/article/details/79129374

- 了解PKG_CONFIG_PATH  看简单介绍 https://blog.csdn.net/MY_RENZHIBO/article/details/51240891

  

### 1. 打开一个普通CMD窗口

`cd C:\msys64 `

> 编辑c:/msys64/msys2_shell.cmd 
> 将rem set MSYS2_PATH_TYPE=inherit的"rem"注释删除

### 2. 我装的是VS2015 对应自己的目录

> 执行 要双引号 

`"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"`



> 使用2步骤跟直接cmd执行下面命令的区别 添加了windows的环境变量到mingw64的cmd 使得mingw64 cmd能使用windows系统的一些可执行程序或库文件



### 3. `msys2_shell.cmd -mingw64`



### 4. `pacman -S diffutils make pkg-config yasm`

### 5. 把cuda安装目录下的bin添加到PATH 照做吧 没有确认第二步骤是否已经添加过 是否此步骤为多余操作 

`export PATH="/c/Program\ Files/NVIDIA\ GPU\ Computing\ Toolkit/CUDA/v10.1/bin/":$PATH`



### 6. 执行

```shell
./configure --enable-nonfree --enable-nvenc --enable-cuda --enable-cuvid --disable-libnpp --extra-cflags=-I../nv_sdk/include --extra-ldflags=-L../nv_sdk/x64
```



提示错误：C compiler test failed.

### 7. 安装GCC

`pacman -S mingw-w64-x86_64-gcc` 

> 一次下载可能不完全，安装多次，每次安装可能下载不完出错，多试几次直到安装完

### 8. 再次执行

```shell
./configure --enable-nonfree --enable-nvenc --enable-cuda --enable-cuvid --disable-libnpp --extra-cflags=-I../nv_sdk/include --extra-ldflags=-L../nv_sdk/x64
```

ERROR: cuda requested, but not all dependencies are satisfied: ffnvcodec

```shell
git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git
cd nv-codec-headers
make
sudo make install
```

- 找到安装cp的ffnvcodec.pc文件和ffnvcodec文件夹 

- echo PKG_CONFIG_PATH找到pkgconfig目录的路径path-a，只需其中一个

- 把ffnvcodec.pc拷贝到path-a目录下

- 拷贝ffnvcodec目录到C:/msys64/mingw64/include目录（这个目录可改变 这个版本的msys64默认就有这个目录）

- 然后编辑ffnvcodec.pc  改为 prefix=C:/msys64/mingw64/ 其中C:/msys64/mingw64/为上面include目录的父目录

### 9. 再次执行

```shell
./configure --enable-nonfree --enable-nvenc --enable-cuda --enable-cuvid --disable-libnpp --extra-cflags=-I../nv_sdk/include --extra-ldflags=-L../nv_sdk/x64
```

### 10. `make`

> 等10几20分钟。。。。。我在虚拟机编译半个多小时。。。

### 11. `make install`



> 拷贝到其他电脑运行要带上几个dll



```
测试能用：
推流
./ffmpeg.exe -re -i video.mp4 -f flv rtmp://127.0.0.1:1935/live/home
播放
./ffplay.exe -fs -i rtmp://127.0.0.1:1935/live/home
```



