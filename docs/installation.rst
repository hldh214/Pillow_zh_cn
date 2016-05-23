安装指引
=========

警告
--------

.. warning:: Pillow 和 PIL 不能同时存在. 请务必在安装Pillow之前卸载PIL.

.. warning:: Pillow >= 1.0 的版本不再支持 "import Image". 请使用 "from PIL import Image" 来代替之.

.. warning:: Pillow >= 2.1.0 的版本不再支持 "import _imaging". 请使用 "from PIL.Image import core as _imaging" 来代替之.

注解
-----

.. note:: Pillow < 2.0.0 支持这些 Python 版本: 2.4, 2.5, 2.6, 2.7.

.. note:: Pillow >= 2.0.0 支持这些 Python 版本: 2.6, 2.7, 3.2, 3.3, 3.4, 3.5

一般环境下安装
------------------

.. note::

    以下教程将会指引你安装支持大多数图片格式的Pillow库, 如需扩展库, 参看这里 :ref:`external-libraries` .

.. note::

    Windows和OS X平台上的普通安装会使用来自PyPI的二进制文件, 如需扩展则需要从源码进行编译.

使用 :command:`pip` 来安装Pillow::

    $ pip install Pillow

或者在 :command:`pip` 无法使用的时候使用 :command:`easy_install` 来安装 `Python Eggs
<http://peak.telecommunity.com/DevCenter/PythonEggs>`_ ::

    $ easy_install Pillow


Windows环境下安装
^^^^^^^^^^^^^^^^^^^^

我们提供了32和64位的wheel, egg和exe安装包来为Windows进行安装, 这些安装包包含了所有的可选扩展库::

  $ pip install Pillow

或::

  $ easy_install Pillow


OS X环境下安装
^^^^^^^^^^^^^^^^^

我们提供了二进制文件来为 OS X 上的所有Python版本进行安装, 这些安装包包含了所有的可选扩展库, 除了OpenJPEG::

  $ pip install Pillow

Linux环境下安装
^^^^^^^^^^^^^^^^^^

我们不为Linux提供二进制文件. 这其中包括绝大多数Linux的衍生版本, 包括Fedora, Debian/Ubuntu 和 ArchLinux 都自带了Pillow库, 比如 ``python-imaging``. 请首先考虑使用系统自带的库, 而不是使用可能出现问题的第三方库.

FreeBSD环境下安装
^^^^^^^^^^^^^^^^^^^^

在FreeBSD上可以使用官方Ports或者包管理系统:

**Ports**::

  $ cd /usr/ports/graphics/py-pillow && make install clean

**包管理系统**::

  $ pkg install py27-pillow

.. note::

    `Pillow FreeBSD port
    <https://www.freshports.org/graphics/py-pillow/>`_ 和扩展包都经过了 ports 团队的测试, 基于所有支持的 FreeBSD 版本, 包括Python 2.x and 3.x.


使用源代码编译
--------------------

下载并解压 `compressed archive from PyPI`_.

.. _compressed archive from PyPI: https://pypi.python.org/pypi/Pillow

.. _external-libraries:

扩展
^^^^^^^^^^^^^^^^^^

.. note::

    当你只需要Pillow的基本功能时 **不需要安装所有扩展依赖** . **Zlib** 和 **libjpeg** 是默认被引入的.

.. note::

    在 ``depends`` 目录下有为某些操作系统安装依赖的脚本文件.

Pillow的许多特性需要扩展库来支持:

* **libjpeg** 提供 JPEG 格式支持.

  * Pillow在 **6b**, **8**, **9**, 和 **9a** 版本的 libjpeg 以及 **8** 版本的 libjpeg-turbo 上测试通过.
  * 如果使用 Pillow 3.0.0, libjpeg 是默认需要的依赖, 但是可能被 ``--disable-jpeg`` 这个选项禁用了.

* **zlib** 提供 PNGs 格式压缩

  * 如果使用 Pillow 3.0.0, zlib 是默认需要的依赖, 但是可能被 ``--disable-zlib`` 这个选项禁用了.

* **libtiff** 提供 TIFF 格式压缩

  * Pillow 在 **3.x** and **4.0** 版本的 libtiff 上测试通过.

* **libfreetype** 提供类型相关支持

* **littlecms** 提供色彩管理支持

  * Pillow 版本在 2.2.1 及以下的使用了 liblcms1, Pillow 版本在 2.3.0 及以上使用了 liblcms2. 在 **1.19** 和 **2.7** 版本下测试通过.

* **libwebp** 提供 WebP 格式支持.

  * Pillow has been tested with version **0.1.3**, which does not read
    transparent WebP files. Versions **0.3.0** and above support
    transparency.

* **tcl/tk** 提供 tkinter bitmap 和 photo images支持.

* **openjpeg** 提供 JPEG 2000 支持.

  * Pillow has been tested with openjpeg **2.0.0** and **2.1.0**.
  * Pillow does **not** support the earlier **1.5** series which ships
    with Ubuntu and Debian.

只要你安装了所需依赖, 执行::

    $ pip install Pillow

如果这些依赖在你的机器上安装好了 (例如. :file:`/usr` 或者 :file:`/usr/local`), 那么在编译的时候不需要加上编译选项. 如果在机器上没有,你则需要编辑 :file:`setup.py` 或者
:file:`setup.cfg` 来设置编译选项, 或者在命令里面加上环境变量::

    $ CFLAGS="-I/usr/pkg/include" pip install pillow

如果Pillow在没有添加依赖的情况下安装过, 可能需要先使用 ``--no-cache-dir`` 来清理pip的缓存, 再指定新的依赖进行安装.


编译选项
^^^^^^^^^^^^^

* Environment Variable: ``MAX_CONCURRENCY=n``. By default, Pillow will
  use multiprocessing to build the extension on all available CPUs,
  but not more than 4. Setting ``MAX_CONCURRENCY`` to 1 will disable
  parallel building.

* Build flags: ``--disable-zlib``, ``--disable-jpeg``,
  ``--disable-tiff``, ``--disable-freetype``, ``--disable-tcl``,
  ``--disable-tk``, ``--disable-lcms``, ``--disable-webp``,
  ``--disable-webpmux``, ``--disable-jpeg2000``. Disable building the
  corresponding feature even if the development libraries are present
  on the building machine.

* Build flags: ``--enable-zlib``, ``--enable-jpeg``,
  ``--enable-tiff``, ``--enable-freetype``, ``--enable-tcl``,
  ``--enable-tk``, ``--enable-lcms``, ``--enable-webp``,
  ``--enable-webpmux``, ``--enable-jpeg2000``. Require that the
  corresponding feature is built. The build will raise an exception if
  the libraries are not found. Webpmux (WebP metadata) relies on WebP
  support. Tcl and Tk also must be used together.

* Build flag: ``--disable-platform-guessing``. Skips all of the
  platform dependent guessing of include and library directories for
  automated build systems that configure the proper paths in the
  environment variables (e.g. Buildroot).

* Build flag: ``--debug``. Adds a debugging flag to the include and
  library search process to dump all paths searched for and found to
  stdout.


Sample Usage::

    $ MAX_CONCURRENCY=1 python setup.py build_ext --enable-[feature] install

or using pip::

    $ pip install pillow --global-option="build_ext" --global-option="--enable-[feature]"


在 OS X 上编译
^^^^^^^^^^^^^^^^

Xcode is required to compile portions of Pillow. Either install the
full package from the app store, or run ``xcode-select --install``
from the command line.  It may be necessary to run ``sudo xcodebuild
-license`` to accept the license prior to using the tools.

The easiest way to install external libraries is via `Homebrew
<http://brew.sh/>`_. After you install Homebrew, run::

    $ brew install libtiff libjpeg webp little-cms2

Install Pillow with::

    $ pip install Pillow

or from within the uncompressed source directory::

    $ python setup.py install

在 Windows 上编译
^^^^^^^^^^^^^^^^^^^

We don't recommend trying to build on Windows. It is a maze of twisty
passages, mostly dead ends. There are build scripts and notes for the
Windows build in the ``winbuild`` directory.

在 FreeBSD 上编译
^^^^^^^^^^^^^^^^^^^

.. Note:: Only FreeBSD 10 tested

Make sure you have Python's development libraries installed.::

    $ sudo pkg install python2

Or for Python 3::

    $ sudo pkg install python3

Prerequisites are installed on **FreeBSD 10** with::

    $ sudo pkg install jpeg tiff webp lcms2 freetype2


在 Linux 上编译
^^^^^^^^^^^^^^^^^

If you didn't build Python from source, make sure you have Python's
development libraries installed. In Debian or Ubuntu::

    $ sudo apt-get install python-dev python-setuptools

Or for Python 3::

    $ sudo apt-get install python3-dev python3-setuptools

In Fedora, the command is::

    $ sudo dnf install python-devel redhat-rpm-config

.. Note:: ``redhat-rpm-config`` is required on Fedora 23, but not earlier versions.

Prerequisites are installed on **Ubuntu 12.04 LTS** or **Raspian Wheezy
7.0** with::

    $ sudo apt-get install libtiff4-dev libjpeg8-dev zlib1g-dev \
        libfreetype6-dev liblcms2-dev libwebp-dev tcl8.5-dev tk8.5-dev python-tk

Prerequisites are installed on **Ubuntu 14.04 LTS** with::

    $ sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev \
        libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk

Prerequisites are installed on **Fedora 23** with::

    $ sudo dnf install libtiff-devel libjpeg-devel zlib-devel freetype-devel \
        lcms2-devel libwebp-devel tcl-devel tk-devel



兼容性
----------------

Current platform support for Pillow. Binary distributions are contributed for
each release on a volunteer basis, but the source should compile and run
everywhere platform support is listed. In general, we aim to support all
current versions of Linux, OS X, and Windows.

.. note::

    Contributors please test Pillow on your platform then update this
    document and send a pull request.

+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
|**Operating system**              |**Supported**|**Tested Python versions**    |**Latest tested Pillow version**|**Tested processors**  |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Mac OS X 10.11 El Capitan        |Yes          | 2.7,3.3,3.4,3.5              | 3.2.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Mac OS X 10.10 Yosemite          |Yes          | 2.7,3.3,3.4                  | 3.0.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Mac OS X 10.9 Mavericks          |Yes          | 2.7,3.2,3.3,3.4              | 3.0.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Mac OS X 10.8 Mountain Lion      |Yes          | 2.6,2.7,3.2,3.3              |                                |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Redhat Linux 6                   |Yes          | 2.6                          |                                |x86                    |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| CentOS 6.3                       |Yes          | 2.7,3.3                      |                                |x86                    |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Fedora 23                        |Yes          | 2.7,3.4                      | 3.1.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Ubuntu Linux 10.04 LTS           |Yes          | 2.6                          | 2.3.0                          |x86,x86-64             |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Ubuntu Linux 12.04 LTS           |Yes          | 2.6,2.7,3.2,3.3,3.4,3.5      | 3.1.0                          |x86,x86-64             |
|                                  |             | PyPy2.4,PyPy3,v2.3           |                                |                       |
|                                  |             |                              |                                |                       |
|                                  |             | 2.7,3.2                      | 2.6.1                          |ppc                    |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Ubuntu Linux 14.04 LTS           |Yes          | 2.7,3.4                      | 3.1.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Debian 8.2 Jessie                |Yes          | 2.7,3.4                      | 3.1.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Raspian Jessie                   |Yes          | 2.7,3.4                      | 3.1.0                          |arm                    |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Gentoo Linux                     |Yes          | 2.7,3.2                      | 2.1.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| FreeBSD 10.2                     |Yes          | 2.7,3.4                      | 3.1.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Windows 7 Pro                    |Yes          | 2.7,3.2,3.3                  | 2.2.1                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Windows Server 2008 R2 Enterprise|Yes          | 3.3                          |                                |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Windows Server 2012 R2           |Yes          | 2.7,3.3,3.4                  | 3.0.0                          |x86-64                 |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Windows 8 Pro                    |Yes          | 2.6,2.7,3.2,3.3,3.4a3        | 2.2.0                          |x86,x86-64             |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+
| Windows 8.1 Pro                  |Yes          | 2.6,2.7,3.2,3.3,3.4          | 2.4.0                          |x86,x86-64             |
+----------------------------------+-------------+------------------------------+--------------------------------+-----------------------+

旧版本
------------

你可以在 `PyPI
<https://pypi.python.org/pypi/Pillow>`_ 下载到旧版本的Pillow. 只有最新的支持 Python 2.x 和 3.x 的版本是可见的, 但是所有的发行版本都可以通过url直接获取到
e.g. https://pypi.python.org/pypi/Pillow/1.0.
