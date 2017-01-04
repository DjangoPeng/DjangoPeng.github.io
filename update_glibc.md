## 升级系统glibc 指导文档

**注意：升级系统的glibc 库是一件风险极高的操作，升级失败会导致系统崩溃和数据丢失。请再次确认是否有其他的代替升级GLIBC的解决方案。**

本文档以RHEL 6.4 （glibc 2.12）为例，将系统GLIBC升级至2.14。

#### 1.查看系统glibc版本

```shell
$ ll /lib64/libc.so.6
lrwxrwxrwx. 1 root root 12 Oct 28 19:18 /lib64/libc.so.6 -> libc-2.12.so
```

#### 2.编译glibc-2.14

```shell
$ tar xvf glibc-2.14.tar.gz
$ tar xvf glibc-ports-2.14.tar.gz
$ mv glibc-ports-2.14 glibc-2.14/ports
$ mkdir glibc-2.14-build
$ cd glibc-2.14-build
# Configure glibc 2.14
$ ../glibc-2.14/configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
# Build 
$ make
```

#### 3.安装glibc-2.14

```shell
# 根据系统的环境不同，安装过程中可能出现错误
$ make install
```

#### 4.检查是否升级成功

```shell
# 如果出现错误，首先查看/lib64下是否安装libc-2.14
$ ll /lib64/libc*
-rwxr-xr-x. 1 root root 1916568 Nov 21  2012 libc-2.12.so
-rwxr-xr-x  1 root root 9643763 Dec 23 17:30 libc-2.14.so
lrwxrwxrwx. 1 root root      18 Aug 15 18:00 libcap-ng.so.0 -> libcap-ng.so.0.0.0
-rwxr-xr-x. 1 root root   18672 Nov  5  2010 libcap-ng.so.0.0.0
lrwxrwxrwx. 1 root root      14 Aug 15 17:59 libcap.so.2 -> libcap.so.2.16
-rwxr-xr-x. 1 root root   16600 Aug 23  2011 libcap.so.2.16
lrwxrwxrwx. 1 root root      19 Aug 15 18:02 libcgroup.so.1 -> libcgroup.so.1.0.37
-rwxr-xr-x. 1 root root   80568 Dec 20  2012 libcgroup.so.1.0.37
-rwxr-xr-x. 1 root root  197064 Nov 21  2012 libcidn-2.12.so
lrwxrwxrwx. 1 root root      15 Aug 15 17:59 libcidn.so.1 -> libcidn-2.12.so
lrwxrwxrwx. 1 root root      17 Aug 15 17:59 libcom_err.so.2 -> libcom_err.so.2.1
-rwxr-xr-x. 1 root root   14664 Oct 13  2012 libcom_err.so.2.1
-rwxr-xr-x. 1 root root   40400 Nov 21  2012 libcrypt-2.12.so
lrwxrwxrwx. 1 root root      22 Aug 15 18:02 libcryptsetup.so.1 -> libcryptsetup.so.1.1.0
-rwxr-xr-x. 1 root root   94312 Feb 29  2012 libcryptsetup.so.1.1.0
lrwxrwxrwx. 1 root root      16 Aug 15 17:59 libcrypt.so.1 -> libcrypt-2.12.so
lrwxrwxrwx  1 root root      19 Dec 23 17:39 libc.so.6 -> /lib64/libc-2.14.so

# 检查软链，编译成功会在glibc-2.14-build/libc.so.6生成软链
$ ll libc.so.6
lrwxrwxrwx 1 root root 7 Dec 23 17:24 libc.so.6 -> libc.so

# 查看系统glibc版本
$ strings libc.so | grep GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_PRIVATE
# 说明glibc-2.14安装成功
```

#### 5.更新系统库

```shell
$ cp libc.so /lib64/libc-2.14.so
# 删除glibc-2.12的软链
$ rm -rf /lib64/libc.so.6
# 生成glibc-2.14的软链
$ LD_PRELOAD=/lib64/libc-2.14.so ln -s /lib64/libc-2.14.so  /lib64/libc.so.6 
```

#### 6.测试glibc-2.14

```python
# 安装tensorflow_0.10后，测试glibc版本依赖
$ python
Python 2.7.12 |Anaconda 4.2.0 (64-bit)| (default, Jul  2 2016, 17:42:40) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-1)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
Anaconda is brought to you by Continuum Analytics.
Please check out: http://continuum.io/thanks and https://anaconda.org
>>>import tensorflow as tf
>>> hello = tf.constant("Hello TensorFlow!")
>>> sess = tf.Session()
>>> sess.run(hello)
'Hello TensorFlow!'
```

