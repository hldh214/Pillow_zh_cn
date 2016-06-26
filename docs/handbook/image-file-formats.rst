.. _image-file-formats:

图片文件格式
==================

Python Imaging Library 支持绝大多数的图片格式. 囊括了30多种.
虽然对写的支持稍微少了一些, 但是大部分的交换格式都是支持的.

:py:meth:`~PIL.Image.Image.open` 方法使用文件内容来区分不同的文件,
除非你指定格式, 否则 :py:meth:`~PIL.Image.Image.save` 方法使用文件名来识别所需格式.


完全支持的格式
-----------------------

BMP
^^^

PIL 在 Windows 和 OS/2 平台上读取 BMP 文件的 ``1``, ``L``, ``P``,
或 ``RGB`` 数据. 而16色图片则读取 ``P`` 数据, 而不支持游程编码.

:py:meth:`~PIL.Image.Image.open` 方法用于设置 :py:attr:`~PIL.Image.Image.info` 的属性:

**压缩**
    如果文件使用游程编码, 请设置 ``bmp_rle`` .

EPS
^^^

PIL 使用图片内容来区分 EPS 文件, 另外还可以读取嵌入的图片.
如果 Ghostscript 可用, 其他的 EPS 文件将会被正常读取. EPS 驱动也可以用于写入 EPS 图像.

如果 Ghostscript 可用, 你可以调用 :py:meth:`~PIL.Image.Image.load` 方法
来渲染 EPS 文件.

**计算**
    影响栅格图像的计算结果. 如果 EPS 建议以 100px x 100px 来渲染的话,
    传入两个参数将会以 200px x 200px 来渲染之. 而图片的相对位置保持不变::

        im = Image.open(...)
        im.size #(100,100)
        im.load(scale=2)
        im.size #(200,200)

GIF
^^^

PIL会读取 GIF87a 和 GIF89a 标准的 GIF 文件. 默认使用 GIF87a 标准的游程编码,
除非用到了 GIF89a 的特性.

值得注意的是, GIF 文件总是以 grayscale (``L``) 或者 palette (``P``) 模式读取.

:py:meth:`~PIL.Image.Image.open` 方法设置了 :py:attr:`~PIL.Image.Image.info` 的这几个属性:

**background**
    默认背景色 (调色板颜色).

**duration**
    两个帧之间的时间间隔 (毫秒计).

**transparency**
    透明度. 当图像不是透明的时候, 这个属性会被忽略.

**version**
    标准 (``GIF87a`` 或 ``GIF89a``).

**duration**
    用于显示 GIF 图像的每一帧, 以毫秒计.(可能被忽略)

**loop**
    GIF 文件的循环次数(可能被忽略).

读取序列
~~~~~~~~~~~~~~~~~

GIF 加载器支持 :py:meth:`~file.seek` 和 :py:meth:`~file.tell` 方法.
你可以转到下一帧 (``im.seek(im.tell() + 1)``), 或者回溯到首帧. 而不支持随机访问.

当你尝试在最后一帧继续使用 ``im.seek()`` 转到下一帧的时候, 将会抛出 ``EOFError`` 异常.

保存序列
~~~~~~~~~~~~~~~~

当你调用:py:meth:`~PIL.Image.Image.save`, 如果使用了一个多帧的图像, 默认只保存首帧.
要想保存所有帧, ``save_all`` 参数必须设置为 ``True``.

在允许条件下, ``loop`` 参数可以设置 GIF 图像的循环次数, ``duration`` 参数则设置了每一帧之间的时间间隔.

从本地读取图像文件
~~~~~~~~~~~~~~~~~~~~

GIF 加载器会分配和GIF文件大小相同的内存空间, 并且加载这些数据进内存.
若你仅仅需要实际的像素, 你可以修改 :py:attr:`~PIL.Image.Image.size` 和 :py:attr:`~PIL.Image.Image.tile`
属性之后再加载图像文件::

    im = Image.open(...)

    if im.tile[0][0] == "gif":
        # only read the first "local image" from this GIF file
        tag, (x0, y0, x1, y1), offset, extra = im.tile[0]
        im.size = (x1 - x0, y1 - y0)
        im.tile = [(tag, (0, 0) + im.size, offset, extra)]

ICNS
^^^^

PIL 仅能在 OS X 下读写 ``.icns`` 文件. 默认情况下, 尽最大可能读取, 不过你也可以修改
:py:attr:`~PIL.Image.Image.size` 属性之后再读取. :py:meth:`~PIL.Image.Image.open`
方法设置了 :py:attr:`~PIL.Image.Image.info` 的如下属性:

**sizes**
    支持的尺寸列表; 由3元素的元组组成, ``(width, height, scale)``, 其中,
    ``scale`` 表示了2个 retina 尺寸和1个标准尺寸. 你 *可以* 设置 :py:attr:`~PIL.Image.Image.size`
    属性之后再调用 :py:meth:`~PIL.Image.Image.load` 方法; 在加载完毕之后,
    尺寸会重置为一个2个元组组成的像素数据. 比如你传入 ``(512, 512, 2)``,
    返回的将会是 ``(1024, 1024)``).

IM
^^

IM 被用于 LabEye 和其他的一些基于 IFUNC 图像处理库的应用. 可以读写大多数未经压缩的图像格式.

IM 是唯一的一个可以存储 PIL 内部格式的格式(好绕口~~~).

JPEG
^^^^

PIL 能读取 JPEG, JFIF, 和包含了 ``L``, ``RGB``, 或 ``CMYK`` 的 Adobe JPEG 数据.
他能写入标准和激进的 JFIF 文件.

使用 :py:meth:`~PIL.Image.Image.draft` 方法, 可以加速转换 ``RGB`` 到 ``L``,
并且可以在加载的时候放缩到原来尺寸的 1/2, 1/4 或者 1/8.

:py:meth:`~PIL.Image.Image.open` 方法用于设置 :py:attr:`~PIL.Image.Image.info` 属性的以下内容:

**jfif**
    JFIF 应用标记, 如果不是一个 JFIF 文件, 则忽略.

**jfif_version**
    表示 JFIF 版本的元组, (主版本, 副版本).

**jfif_density**
    表示图像的密度的元组, 以 jfif_unit 的方式表示.

**jfif_unit**
    jfif_density 的单位:

    * 0 - 没有单位
    * 1 - 像素每英尺
    * 2 - 像素每厘米

**dpi**
    表示 dpi 的元组, 如果是 JFIF 文件则单位是英尺.

**adobe**
    Adobe 文件标记. 如果不是一个 Adobe 文件, 则忽略.

**adobe_transform**
    厂商定义标签.

**progression**
    标志着这是一个激进类型的 JPEG 文件.

**icc_profile**
    图像的 ICC 颜色属性.

**exif**
    图像的原始 EXIF 数据.


:py:meth:`~PIL.Image.Image.save` 方法支持以下参数:

**quality**
    图像的质量, 从1(最差)到95(最好). 默认是75. 应该避免赋95以上的值;
    100禁用了 JPEG 部分压缩算法, 将会导致大文件图像质量降低.

**optimize**
    如果提供了这个参数, 表示了编码器应该加载额外的编码器来进行相应操作.

**progressive**
    如果提供了这个参数, 表示这个图像将会以激进模式存储.

**dpi**
    表示 DPI 的元组, ``(x,y)``.

**icc_profile**
    如果提供了这个参数, 图像将会以提供的 ICC profile 存储.
    如果这个参数没有定义, 则图像不会包含任何 profile, 看这个例子::

        im.save(filename, 'jpeg', icc_profile=im.info.get('icc_profile'))

**exif**
    如果提供了这个参数, 图像将会以提供的 EXIF 信息存储.

**subsampling**
    如果提供了这个参数, 将会为解码器设置子采样.

    * ``keep``: 只针对 JPEG 文件, 将会保持原文件属性.
    * ``4:4:4``, ``4:2:2``, ``4:1:1``: 指定的数据
    * ``-1``: 等同于 ``keep``
    * ``0``: 等同于 ``4:4:4``
    * ``1``: 等同于 ``4:2:2``
    * ``2``: 等同于 ``4:1:1``

**qtables**
    如果提供了这个参数, 将会设置编码器为 qtables. 这是罗列在 JPEG 文档中的高级选项.
    请谨慎使用. ``qtables`` 可以设置如下几个值:

    *  预设的字符串名称, 例如 ``keep``, ``web_low``, 或 ``web_high``
    *  一个列表, 元组或者字典.

    .. versionadded:: 2.5.0


.. note::

    要想添加 JPEG 支持, 你需要编译安装 IJG JPEG 库之后才能编译 PIL. 详见 distribution README.

JPEG 2000
^^^^^^^^^

.. versionadded:: 2.4.0

PIL 可以读写包含了 ``L``, ``LA``, ``RGB`` 或 ``RGBA`` 数据的 JPEG 2000 文件.
同时也可以读取 ``YCbCr`` 数据, 并且会因为是否有 alpha 频段转换 ``RGB`` 或 ``RGBA``.
PIL 支持原生 JPEG 2000 (``.j2k`` files), 也就是 (``.j2p`` or ``.jpx`` 文件).
PIL *不能* 支持包含不同样本频段的文件.

在加载的时候, 如果设置了 :py:meth:`~PIL.Image.Image.load` 方法的 ``mode`` 属性,
你可以干预 PIL 选择转换成 ``RGB`` 或者 ``RGBA``. 同时也可以设置 ``reduce`` 属性
来忽略一些东西, ``layers`` 属性指定了加载的质量.

:py:meth:`~PIL.Image.Image.save` 方法支持以下选项:

**offset**
    图像的起始位置, 以元组表示, 例如 (16, 16)

**tile_offset**
    材质起始位置, 以2个元素的元组表示.

**tile_size**
    材质大小以2元素元组表示. 如果没有指定, 亦或者设置为 None, 则不包含材质来保存图片.

**quality_mode**
    `"rates"` 和 `"dB"` 都取决于你定义的图像的质量.

**quality_layers**
    一个数字序列, 其值为噪点的近似值. 如果未指定, 默认为原始的质量.

**num_resolutions**
    图像存储的分辨率的数量.

**codeblock_size**
    这是一个2元组, 最小大小为 4 x 4, 最大为 1024 x 1024, 如果不是代码块则有 4096 种参数.

**precinct_size**
    区域大小是一个2元素元组, 必须沿着两个坐标轴, 并且是代码块大小.

**irreversible**
    如果值为 ``True``, 使用遵循 DWT 9-7 的有损图像转换. 默认值为 ``False``, 代表了 DWT 5-3 标准的可逆图像转换.

**progression**
    控制主次顺序; 只能为 ``"LRCP"``, ``"RLCP"``, ``"RPCL"``, ``"PCRL"``, ``"CPRL"``.
    这些字母分别代表了组件, 位置, 分辨率, 和分层控制顺序, 举个例子吧.
    一个以 LRCP 模式做解码的图像能在质量层做解码, 而 RLCP 模式则可以使用增加分辨率解码器, 等等.

**cinema_mode**
    设置编码器为数字影院格式. 默认为否, ``"cinema2k-24"`` 是 24fps 2K,
    ``"cinema2k-48"`` 是 48fps 2K, ``"cinema4k-24"`` 是 24fps 4K.
    值得注意的是, 要想兼容 2K 文件, *至少* 需要符合 2048 x 1080,
    要想兼容 4K 文件, *至少* 需要符合 4096 x 2160.

.. note::

    要想启用 JPEG 2000 支持, 你需要在编译 PIL 之前编译安装2.0.0或更高版本的 OpenJPEG 库.

    Windows 用户可以在 OpenJPEG 网站上下载到二进制安装包进行安装, 但是必须添加到环境变量才行.

MSP
^^^

PIL 在Windows1 和2定义了 MSP 文件. 会写入未经压缩的版本.

PCX
^^^

PIL 可以读写包含了 ``1``, ``L``, ``P``, 或 ``RGB`` 数据的 PCX 文件.

PNG
^^^

PIL 可以识别和读写含有 ``1``, ``L``, ``P``, ``RGB``, 或 ``RGBA`` 数据的 PNG 文件.
自从 v1.1.7 版本之后支持隔行扫描.

:py:meth:`~PIL.Image.Image.open` 方法为 :py:attr:`~PIL.Image.Image.info` 设置了以下属性:

**gamma**
    Gamma值, 应为浮点数.

**transparency**
    对于 ``p`` 图像而言: 调色板不论是全透明亦或者是含有 alpha 值都有索引值.

    对于 ``L`` 和 ``RGB`` 图像来说, 这表示了图像里面的透明像素点.

    如果图像不是透明的, 则会被忽略.

``Open`` 也会给 ``Image.text`` 赋 ``tEXt``, ``zTXt``, 和 ``iTXt`` 的值.
个别压缩格式也有解压大小限制 ``PngImagePlugin.MAX_TEXT_CHUNK``, 默认是 1MB 以防 Zip 炸弹.
另外, 总大小限制为 ``PngImagePlugin.MAX_TEXT_MEMORY``, 默认是 64MB.

The :py:meth:`~PIL.Image.Image.save` method supports the following options:

**optimize**
    If present, instructs the PNG writer to make the output file as small as
    possible. This includes extra processing in order to find optimal encoder
    settings.

**transparency**
    For ``P``, ``L``, and ``RGB`` images, this option controls what
    color image to mark as transparent.

    For ``P`` images, this can be a either the palette index,
    or a byte string with alpha values for each palette entry.

**dpi**
    A tuple of two numbers corresponding to the desired dpi in each direction.

**pnginfo**
    A :py:class:`PIL.PngImagePlugin.PngInfo` instance containing text tags.

**compress_level**
    ZLIB compression level, a number between 0 and 9: 1 gives best speed,
    9 gives best compression, 0 gives no compression at all. Default is 6.
    When ``optimize`` option is True ``compress_level`` has no effect
    (it is set to 9 regardless of a value passed).

**bits (experimental)**
    For ``P`` images, this option controls how many bits to store. If omitted,
    the PNG writer uses 8 bits (256 colors).

**dictionary (experimental)**
    Set the ZLIB encoder dictionary.

.. note::

    To enable PNG support, you need to build and install the ZLIB compression
    library before building the Python Imaging Library. See the distribution
    README for details.

PPM
^^^

PIL reads and writes PBM, PGM and PPM files containing ``1``, ``L`` or ``RGB``
data.

SPIDER
^^^^^^

PIL reads and writes SPIDER image files of 32-bit floating point data
("F;32F").

PIL also reads SPIDER stack files containing sequences of SPIDER images. The
:py:meth:`~file.seek` and :py:meth:`~file.tell` methods are supported, and
random access is allowed.

The :py:meth:`~PIL.Image.Image.open` method sets the following attributes:

**format**
    Set to ``SPIDER``

**istack**
    Set to 1 if the file is an image stack, else 0.

**nimages**
    Set to the number of images in the stack.

A convenience method, :py:meth:`~PIL.Image.Image.convert2byte`, is provided for
converting floating point data to byte data (mode ``L``)::

    im = Image.open('image001.spi').convert2byte()

Writing files in SPIDER format
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The extension of SPIDER files may be any 3 alphanumeric characters. Therefore
the output format must be specified explicitly::

    im.save('newimage.spi', format='SPIDER')

For more information about the SPIDER image processing package, see the
`SPIDER homepage`_ at `Wadsworth Center`_.

.. _SPIDER homepage: http://spider.wadsworth.org/spider_doc/spider/docs/spider.html
.. _Wadsworth Center: http://www.wadsworth.org/

TIFF
^^^^

PIL reads and writes TIFF files. It can read both striped and tiled images,
pixel and plane interleaved multi-band images, and either uncompressed, or
Packbits, LZW, or JPEG compressed images.

If you have libtiff and its headers installed, PIL can read and write many more
kinds of compressed TIFF files. If not, PIL will always write uncompressed
files.

The :py:meth:`~PIL.Image.Image.open` method sets the following
:py:attr:`~PIL.Image.Image.info` properties:

**compression**
    Compression mode.

    .. versionadded:: 2.0.0

**dpi**
    Image resolution as an ``(xdpi, ydpi)`` tuple, where applicable. You can use
    the :py:attr:`~PIL.Image.Image.tag` attribute to get more detailed
    information about the image resolution.

    .. versionadded:: 1.1.5

**resolution**
    Image resolution as an ``(xres, yres)`` tuple, where applicable. This is a
    measurement in whichever unit is specified by the file.

    .. versionadded:: 1.1.5


The :py:attr:`~PIL.Image.Image.tag_v2` attribute contains a dictionary
of TIFF metadata. The keys are numerical indexes from
:py:attr:`~PIL.TiffTags.TAGS_V2`.  Values are strings or numbers for single
items, multiple values are returned in a tuple of values. Rational
numbers are returned as a :py:class:`~PIL.TiffImagePlugin.IFDRational`
object.

    .. versionadded:: 3.0.0

For compatibility with legacy code, the
:py:attr:`~PIL.Image.Image.tag` attribute contains a dictionary of
decoded TIFF fields as returned prior to version 3.0.0.  Values are
returned as either strings or tuples of numeric values. Rational
numbers are returned as a tuple of ``(numerator, denominator)``.

    .. deprecated:: 3.0.0


Saving Tiff Images
~~~~~~~~~~~~~~~~~~

The :py:meth:`~PIL.Image.Image.save` method can take the following keyword arguments:

**tiffinfo**
    A :py:class:`~PIL.TiffImagePlugin.ImageFileDirectory_v2` object or dict
    object containing tiff tags and values. The TIFF field type is
    autodetected for Numeric and string values, any other types
    require using an :py:class:`~PIL.TiffImagePlugin.ImageFileDirectory_v2`
    object and setting the type in
    :py:attr:`~PIL.TiffImagePlugin.ImageFileDirectory_v2.tagtype` with
    the appropriate numerical value from
    ``TiffTags.TYPES``.

    .. versionadded:: 2.3.0

    Metadata values that are of the rational type should be passed in
    using a :py:class:`~PIL.TiffImagePlugin.IFDRational` object.

    .. versionadded:: 3.1.0

    For compatibility with legacy code, a
    :py:class:`~PIL.TiffImagePlugin.ImageFileDirectory_v1` object may
    be passed in this field. However, this is deprecated.

    .. versionadded:: 3.0.0

 .. note::

    Only some tags are currently supported when writing using
    libtiff. The supported list is found in
    :py:attr:`~PIL:TiffTags.LIBTIFF_CORE`.

**compression**
    A string containing the desired compression method for the
    file. (valid only with libtiff installed) Valid compression
    methods are: ``None``, ``"tiff_ccitt"``, ``"group3"``,
    ``"group4"``, ``"tiff_jpeg"``, ``"tiff_adobe_deflate"``,
    ``"tiff_thunderscan"``, ``"tiff_deflate"``, ``"tiff_sgilog"``,
    ``"tiff_sgilog24"``, ``"tiff_raw_16"``

These arguments to set the tiff header fields are an alternative to
using the general tags available through tiffinfo.

**description**

**software**

**date_time**

**artist**

**copyright**
    Strings

**resolution_unit**
    A string of "inch", "centimeter" or "cm"

**resolution**

**x_resolution**

**y_resolution**

**dpi**
    Either a Float, 2 tuple of (numerator, denominator) or a
    :py:class:`~PIL.TiffImagePlugin.IFDRational`. Resolution implies
    an equal x and y resolution, dpi also implies a unit of inches.


WebP
^^^^

PIL reads and writes WebP files. The specifics of PIL's capabilities with this
format are currently undocumented.

The :py:meth:`~PIL.Image.Image.save` method supports the following options:

**lossless**
    If present, instructs the WEBP writer to use lossless
    compression.

**quality**
    Integer, 1-100, Defaults to 80. Sets the quality level for
    lossy compression.

**icc_procfile**
    The ICC Profile to include in the saved file. Only supported if
    the system webp library was built with webpmux support.

**exif**
    The exif data to include in the saved file. Only supported if
    the system webp library was built with webpmux support.

XBM
^^^

PIL reads and writes X bitmap files (mode ``1``).

Read-only formats
-----------------

CUR
^^^

CUR is used to store cursors on Windows. The CUR decoder reads the largest
available cursor. Animated cursors are not supported.

DCX
^^^

DCX is a container file format for PCX files, defined by Intel. The DCX format
is commonly used in fax applications. The DCX decoder can read files containing
``1``, ``L``, ``P``, or ``RGB`` data.

When the file is opened, only the first image is read. You can use
:py:meth:`~file.seek` or :py:mod:`~PIL.ImageSequence` to read other images.


DDS
^^^

DDS is a popular container texture format used in video games and natively
supported by DirectX.
Currently, only DXT1 and DXT5 pixel formats are supported and only in ``RGBA``
mode.


FLI, FLC
^^^^^^^^

PIL reads Autodesk FLI and FLC animations.

The :py:meth:`~PIL.Image.Image.open` method sets the following
:py:attr:`~PIL.Image.Image.info` properties:

**duration**
    The delay (in milliseconds) between each frame.

FPX
^^^

PIL reads Kodak FlashPix files. In the current version, only the highest
resolution image is read from the file, and the viewing transform is not taken
into account.

.. note::

    To enable full FlashPix support, you need to build and install the IJG JPEG
    library before building the Python Imaging Library. See the distribution
    README for details.

FTEX
^^^^

.. versionadded:: 3.2.0

The FTEX decoder reads textures used for 3D objects in
Independence War 2: Edge Of Chaos. The plugin reads a single texture
per file, in the compressed and uncompressed formats.

GBR
^^^

The GBR decoder reads GIMP brush files, version 1 and 2.

The :py:meth:`~PIL.Image.Image.open` method sets the following
:py:attr:`~PIL.Image.Image.info` properties:

**comment**
    The brush name.

**spacing**
    The spacing between the brushes, in pixels. Version 2 only.

GD
^^

PIL reads uncompressed GD files. Note that this file format cannot be
automatically identified, so you must use :py:func:`PIL.GdImageFile.open` to
read such a file.

The :py:meth:`~PIL.Image.Image.open` method sets the following
:py:attr:`~PIL.Image.Image.info` properties:

**transparency**
    Transparency color index. This key is omitted if the image is not
    transparent.

ICO
^^^

ICO is used to store icons on Windows. The largest available icon is read.

The :py:meth:`~PIL.Image.Image.save` method supports the following options:

**sizes**
    A list of sizes including in this ico file; these are a 2-tuple,
    ``(width, height)``; Default to ``[(16, 16), (24, 24), (32, 32), (48, 48),
    (64, 64), (128, 128), (255, 255)]``. Any size is bigger then the original
    size or 255 will be ignored.

IMT
^^^

PIL reads Image Tools images containing ``L`` data.

IPTC/NAA
^^^^^^^^

PIL provides limited read support for IPTC/NAA newsphoto files.

MCIDAS
^^^^^^

PIL identifies and reads 8-bit McIdas area files.

MIC
^^^

PIL identifies and reads Microsoft Image Composer (MIC) files. When opened, the
first sprite in the file is loaded. You can use :py:meth:`~file.seek` and
:py:meth:`~file.tell` to read other sprites from the file.

MPO
^^^

Pillow identifies and reads Multi Picture Object (MPO) files, loading the primary
image when first opened. The :py:meth:`~file.seek` and :py:meth:`~file.tell`
methods may be used to read other pictures from the file. The pictures are
zero-indexed and random access is supported.

PCD
^^^

PIL reads PhotoCD files containing ``RGB`` data. By default, the 768x512
resolution is read. You can use the :py:meth:`~PIL.Image.Image.draft` method to
read the lower resolution versions instead, thus effectively resizing the image
to 384x256 or 192x128. Higher resolutions cannot be read by the Python Imaging
Library.

PIXAR
^^^^^

PIL provides limited support for PIXAR raster files. The library can identify
and read “dumped” RGB files.

The format code is ``PIXAR``.

PSD
^^^

PIL identifies and reads PSD files written by Adobe Photoshop 2.5 and 3.0.

SGI
^^^

PIL reads uncompressed ``L``, ``RGB``, and ``RGBA`` files.

TGA
^^^

PIL reads 24- and 32-bit uncompressed and run-length encoded TGA files.

WAL
^^^

.. versionadded:: 1.1.4

PIL reads Quake2 WAL texture files.

Note that this file format cannot be automatically identified, so you must use
the open function in the :py:mod:`~PIL.WalImageFile` module to read files in
this format.

By default, a Quake2 standard palette is attached to the texture. To override
the palette, use the putpalette method.

XPM
^^^

PIL reads X pixmap files (mode ``P``) with 256 colors or less.

The :py:meth:`~PIL.Image.Image.open` method sets the following
:py:attr:`~PIL.Image.Image.info` properties:

**transparency**
    Transparency color index. This key is omitted if the image is not
    transparent.

Write-only formats
------------------

PALM
^^^^

PIL provides write-only support for PALM pixmap files.

The format code is ``Palm``, the extension is ``.palm``.

PDF
^^^

PIL can write PDF (Acrobat) images. Such images are written as binary PDF 1.1
files, using either JPEG or HEX encoding depending on the image mode (and
whether JPEG support is available or not).

When calling :py:meth:`~PIL.Image.Image.save`, if a multiframe image is used,
by default, only the first image will be saved. To save all frames, each frame
to a separate page of the PDF, the ``save_all`` parameter must be present and
set to ``True``.

XV Thumbnails
^^^^^^^^^^^^^

PIL can read XV thumbnail files.

Identify-only formats
---------------------

BUFR
^^^^

.. versionadded:: 1.1.3

PIL provides a stub driver for BUFR files.

To add read or write support to your application, use
:py:func:`PIL.BufrStubImagePlugin.register_handler`.

FITS
^^^^

.. versionadded:: 1.1.5

PIL provides a stub driver for FITS files.

To add read or write support to your application, use
:py:func:`PIL.FitsStubImagePlugin.register_handler`.

GRIB
^^^^

.. versionadded:: 1.1.5

PIL provides a stub driver for GRIB files.

The driver requires the file to start with a GRIB header. If you have files
with embedded GRIB data, or files with multiple GRIB fields, your application
has to seek to the header before passing the file handle to PIL.

To add read or write support to your application, use
:py:func:`PIL.GribStubImagePlugin.register_handler`.

HDF5
^^^^

.. versionadded:: 1.1.5

PIL provides a stub driver for HDF5 files.

To add read or write support to your application, use
:py:func:`PIL.Hdf5StubImagePlugin.register_handler`.

MPEG
^^^^

PIL identifies MPEG files.

WMF
^^^

PIL can identify placable WMF files.

In PIL 1.1.4 and earlier, the WMF driver provides some limited rendering
support, but not enough to be useful for any real application.

In PIL 1.1.5 and later, the WMF driver is a stub driver. To add WMF read or
write support to your application, use
:py:func:`PIL.WmfImagePlugin.register_handler` to register a WMF handler.

::

    from PIL import Image
    from PIL import WmfImagePlugin

    class WmfHandler:
        def open(self, im):
            ...
        def load(self, im):
            ...
            return image
        def save(self, im, fp, filename):
            ...

    wmf_handler = WmfHandler()

    WmfImagePlugin.register_handler(wmf_handler)

    im = Image.open("sample.wmf")

