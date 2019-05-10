---
title: (转载)linux下R安装(无root权限)
categories: 
  - R
tags:
  - 编程语言
  - R
  - 统计语言
date: 2018-02-13
---
##### R依赖库的安装
在安装R之前首先要安装R依赖的包，如何知道R安装需要什么包呢？可以去看R的官方文档[R Installation and Administration](https://cran.r-project.org/doc/manuals/r-release/R-admin.html#Essential-and-useful-other-programs-under-a-Unix_002dalike) ,我们主要要安装的依赖包是zlib，bzip，liblzma(这个包如果直接搜索liblzma出来的会是XZ，没错，需要安装的就是XZ)以及PCRE。不过首先需要注意的是，我安装的R版本是R-3.4.2 Short Summer，2017-09-28发行，而依照的官方文档也是Version 3.4.2 2017-09-28，因此其他版本的不一定完全适用，但是一般来说R所依赖的包大致都是这些，可能有版本的区别，安装R的大致顺序也差不多。其次，在安装这些库时最好遵照我下面给出的顺序来依次进行。否则就会报错。

1. 安装zlib1.2.5版本及以上
```
cd zlib-1.2.11
./configure --prefix =/bulider/software/zlib
make  
make install
```
2. 安装bzip
```
cd bzip2-1.0.6
make -f Makefile-libbz2_so
#修改Makefile 中的PREFIX=/bulider/software/bzip2
make && make install
```
3. 安装liblzma5.0.3版本及以上
```
cd xz-5.2.3
./configure -prefix=/bulider/software/xz
make
make install
```
4. 安装pcre
```
tar -zxvf pcre-8.41.tar.gz
cd pcre-8.41
./configure --enable-utf --enable-unicode-properties --enable-jit --disable-cpp --prefix /bulider/software/pcre
##这个包的安装参数是有要求的，因此用的是官方文档的推荐命令
make
make install

```
5. 安装libcurl7.22.0及以上，但是不要超过版本8
```
tar -zxf curl-7.28.0.tar.gz
cd curl-7.28.0
./configure --prefix=/bulider/software/curl
make
make install
```
##### 安装R
1. 进行configure
```
/source/R-3.4.2/configure --prefix=/builder/software/R --en
able-R-shlib --with-cairo  --with-jpeglib --with-readline --with-tcltk  --with-blas --with-lapack --enable-R-profiling LDFLAGS="-L/builder/software/zlib/lib  -L/builder/software/bzip2/lib -L/builder/software/xz/lib -L/builder/software/pcre/lib -L/builder/so
ftware/curl/lib" CPPFLAGS="-I/builder/zlib/include -I/builder/softw
are/bzip2/include -I/builder/software/xz/include -I/builder/software/pcre/in
clude -I/builder/software/curl/include"##第一步检查环境配置，生成Makefile

```
首先注意一点，如果你要把R安装到另一个文件夹里而不是源码所在的文件夹里，那么你首先要创建一个文件夹，然后在这个文件夹里对R进行编译，在`configure`时要加上全路径`/source/R-3.4.2/configure`，同时在后面加上`--prefix=`参数，比方我要安装到`/builder/software/R`,那么就需要加入`--prefix=/builder/software/R`这个参数，而我的源码在`/source/R-3.4.2`这个文件夹里。

其次由于R是用C语言写的，因此要从源码编译R需要把R的源码需要通过gcc将源码编译为可执行文件。在前面我们安装了一些R的依赖库，这些依赖库是在编译R的时候要用到的，如果在一般情况下这些依赖库都会放到系统指定的位置来进行使用，但是由于我没有root权限，因此无法将这些库文件放到系统指定的库路径或者修改相应的配置文件，只能把这些库的路径通过`LDFLAGS`和`CPPFLAGS`这两个参数来传递给gcc，关于这两个参数可以参考。告诉gcc这些路径里有相应的库。其中`LDFLAGS`传递的是给链接器的参数，`CPPFLAGS`传递的是相应的头文件。这两个参数都是`configure`的参数。
2. 通过上一步生成了`Makefile`后，觉得胜利已经在前方向你招手，于是你进行了
```
make
```
但是出现了报错！
```
sr/bin/ld: warning: libpcre.so.1, needed by ../../lib/libR.so, not found (try using -rpath or -rpath-link)
/usr/bin/ld: warning: liblzma.so.5, needed by ../../lib/libR.so, not found (try using -rpath or -rpath-link)
../../lib/libR.so: undefined reference to `pcre_fullinfo'
../../lib/libR.so: undefined reference to `lzma_lzma_preset@XZ_5.0'
../../lib/libR.so: undefined reference to `lzma_alone_decoder@XZ_5.0'
../../lib/libR.so: undefined reference to `lzma_crc64@XZ_5.0'
../../lib/libR.so: undefined reference to `lzma_raw_encoder@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_config'
../../lib/libR.so: undefined reference to `lzma_code@XZ_5.0'
../../lib/libR.so: undefined reference to `lzma_stream_decoder@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_free'
../../lib/libR.so: undefined reference to `lzma_raw_decoder@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_free_study'
../../lib/libR.so: undefined reference to `pcre_assign_jit_stack'
../../lib/libR.so: undefined reference to `pcre_exec'
../../lib/libR.so: undefined reference to `lzma_version_string@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_maketables'
../../lib/libR.so: undefined reference to `lzma_stream_encoder@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_compile'
../../lib/libR.so: undefined reference to `pcre_study'
../../lib/libR.so: undefined reference to `pcre_version'
../../lib/libR.so: undefined reference to `lzma_end@XZ_5.0'
../../lib/libR.so: undefined reference to `pcre_jit_stack_alloc'
collect2: error: ld returned 1 exit status
make[3]: *** [R.bin] Error 1
make[3]: Leaving directory `/builder/software/R/src/main'
make[2]: *** [R] Error 2
make[2]: Leaving directory `/builder/software/R/src/main'
make[1]: *** [R] Error 1
make[1]: Leaving directory `/builder/software/R/src'
make: *** [R] Error 1
```
这个报错是因为什么原因导致的？我们可以看到里面说
```
usr/bin/ld: warning: libpcre.so.1, needed by ../../lib/libR.so, not found (try using -rpath or -rpath-link)
/usr/bin/ld: warning: liblzma.so.5, needed by ../../lib/libR.so, not found (try using -rpath or -rpath-link)
```
说缺少`libpcre.so.1`和`liblzma.so.5`这两个库文件，我心里浮现了一副黑人问号脸我在前面`./configure`的时候还传入了`PCRE`和`XZ`这两个库的路径，为什么还是会报错？
于是我先是进行了谷歌，从谷歌上找到了一篇R安装攻略,在这篇攻略里也提到了这个问题，


>此处报错是由于没有找到动态库，看第一二行
>
>解决方法：添加动态库
>
>cat /etc/ld.so.conf
>
>include ld.so.conf.d/*.conf
>
>/opt/pcre-8.39/lib
>
>/opt/xz-5.2.2/lib

但是在这个攻略里最后的解决方法是修改`/etc/ld.so.conf`文件，将前面安装的库的路径添加到这个文件中，但是我没有root权限，并不能修改这个文件，因此这个方法对我并不适用。后来在鹏帆和许骄的提醒下，我们在后面看到建议说try using `-rpath or -rpath-link`,后来我搜索了这两个命令，这两个命令是用于通过gcc向ld传递参数的时候用的，
>gcc编译链接动态库时，很有可能编译通过，但是执行时，找不到动态链接库，那是因为-L选项指定的路径只在编译时有效，解决方法是通过-Wl(注意，是字母表K之后的小写L),`rpath=<your_lib_dir>`，使得execute记住链接库的位置

于是我试着在configure的时候加入这两个参数
```
/builder/software/R-3.4.2/configure --prefix=/builder/software/R --en
able-R-shlib --with-cairo  --with-jpeglib --with-readline --with-tcltk  --with-blas --with-lapack --enable-R-profiling LDFLAGS="-L/builder/software/zlib/lib  -L/builder/software/bzip2/lib -L/builder/software/xz/lib  -Wl,--rpath/builder/software/pcre/lib -Wl,--rpath/builder/so
ftware/curl/lib" CPPFLAGS="-I/builder/software/zlib/include -I/builder/softw
are/bzip2/include -I/builder/software/xz/include -I/builder/software/pcre/in
clude -I/builder/software/curl/include
```
但是这样总会报错，后来我知道，configure的时候我们传入的参数都会写到Makeconf文件里面，那么我可不可以先按照原来的我方法先进行configure，然后再对Makeconf文件进行修改呢？于是我还是先按照之前的方法进行configure
```
/builder/software/R-3.4.2/configure --prefix=/builder/software/R --en
able-R-shlib --with-cairo  --with-jpeglib --with-readline --with-tcltk  --with-blas --with-lapack --enable-R-profiling LDFLAGS="-L/builder/software/zlib/lib  -L/builder/software/bzip2/lib -L/builder/software/xz/lib -L/builder/software/pcre/lib -L/builder/so
ftware/curl/lib" CPPFLAGS="-I/builder/software/zlib/include -I/builder/softw
are/bzip2/include -I/builder/software/xz/include -I/builder/software/pcre/in
clude -I/builder/software/curl/include
```
然后在make之前修改Makeconf中的参数
```
LDFLAGS = -L/builder/software/zlib/lib  -L/builder/software/bzip2/lib   -L/builder/software/curl/lib -Wl,-rpath=/builder/software/xz/lib -Wl,-rpath=/builder/software/pcre/lib
```
再进行
```
make
```
之前的报错终于不见了!但是出现了新的报错心里有句mmp:
```
/usr/bin/ld: cannot find -lpcre
collect2: error: ld returned 1 exit status
make[3]: *** [libR.so] Error 1
make[3]: Leaving directory `/builder/software/R-3.4.2/src/main'
make[2]: *** [R] Error 2
make[2]: Leaving directory `/builder/software/R-3.4.2/src/main'
make[1]: *** [R] Error 1
make[1]: Leaving directory `/builder/software/R-3.4.2/src'
make: *** [R] Error 1
```
于是我在想，可能是前面我把LDFLAGS里面参数由-L/builder/software/pcre/lib修改为了-Wl,-rpath=/builder/software/pcre/lib之后不再出现原来的报错，但是现在又出现了新的报错，而我在使用原来的参数时不会出现这个新的报错，结合之前我搜索到的结果,是不是因为我之前的写法只能让路径gcc在编译时有效,而后来的写法只能让其在进行运行时有效？那么如果我让两种参数同时出现，虽然我把库的路径传递了两次，但是会不会就不再报错了？时间是检验真理的唯一标准！先试验一把！于是我把两种参数的路径都加到了Makeconf中
```
LDFLAGS = -L/builder/software/zlib/lib  -L/builder/software/bzip2/lib -L/builder/software/xz/lib -L/builder/software/pcre/lib -L/builder/software/curl/lib -Wl,-rpath=/builder/software/xz/lib -Wl,-rpath=/builder/software/pcre/lib
```
再次进行
```
make
```
终于没有了报错。说明make成功了，于是我再进行
```
make install
```
终于安装完成！你挑着担我牵着马

##### 总结

1.在Linux下用源码安装R是个大坑尤其是在没有root权限的情况下

2.安装R之前一定要将R要求的依赖库预先安装好，版本和安装顺序都要对。具体可以参考R官方文档R Installation and Administration 。

3.在安装好R的依赖库之后，在进行R的安装的时候要注意将依赖库的路径传入到参数中，而且一定要注意传入的方式。

4.注意分析报错信息，从中寻找原因，不要盲目瞎想，多谷歌，多问老司机。

5.关于-Wl -rpath更详细的介绍[参考](https://my.oschina.net/shelllife/blog/115958)



3.在安装好R的依赖库之后，在进行R的安装的时候要注意将依赖库的路径传入到参数中，而且一定要注意传入的方式。
4.注意分析报错信息，从中寻找原因，不要盲目瞎想，多谷歌，多问老司机。
5.关于-Wl -rpath更详细的介绍参考

参考链接：http://www.jianshu.com/p/5bfe154f1aa4
