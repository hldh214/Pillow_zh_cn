教程
==============

使用 Image 类
--------------

在Python中最重要的图像处理库非 :py:class:`~PIL.Image.Image` 莫属,
以同样的名字定义模块, 你能多样的实例化这个类, 无论是从文件读取图片,
亦或者是处理其他图片, 或者随心所欲的绘制你所想到的图形.

要想以文件的方式读取图片, 使用来自 :py:mod:`~PIL.Image` 模块的 :py:func:`~PIL.Image.open` 方法 ::

    >>> from PIL import Image
    >>> im = Image.open("lena.ppm")

如果操作成功, 这个方法返回一个 :py:class:`~PIL.Image.Image` 对象.
之后你就可以使用这个对象的方法去查看图片的属性 ::

    >>> from __future__ import print_function
    >>> print(im.format, im.size, im.mode)
    PPM (512, 512) RGB

:py:attr:`~PIL.Image.Image.format` 这个属性代表图片文件的扩展名,
如果图片文件打开失败, 则其值为None.  :py:attr:`~PIL.Image.Image.size`
这个属性代表图片的大小, 以像素为单位, 使用包含两个元素的元组来返回.
:py:attr:`~PIL.Image.Image.mode` 这个属性代表图片的band属性,
一般情况(黑白)下为 "L", 当图片是彩色的时候是 "RGB", 如果图片经过压缩,
则是 "CMYK".

如果图片文件打开失败, 将会提示 :py:exc:`IOError` 错误.

一旦你实例化了 :py:class:`~PIL.Image.Image` 类, 你就可以使用该类的方法去处理图像.
例如, 我们直接显示这个刚刚被加载的图像::

    >>> im.show()

.. note::

    从保存图片到缓存文件到调用 :command:`xv` 命令去显示这个图片,
    :py:meth:`~PIL.Image.Image.show` 的效率很低. 另外,
    如果你根本没有安装 :command:`xv` 命令, 这个方法就无法使用.
    即便可以使用, 在调试的时候也是犹如噩梦一般的存在.

接下来的几个小结将会向你们介绍这个库的几个不同的方法.

图片的读写操作
--------------------------

Python的图像处理库支持绝大多数的图片格式. 直接使用来自 :py:mod:`~PIL.Image`
模块的 :py:func:`~PIL.Image.open` 方法就能从硬盘读取图片文件.
不需要你来区分不同的图片格式, 这个库会自动匹配对应的解码器来打开图片文件.

直接使用来自 :py:mod:`~PIL.Image` 模块的 :py:func:`~PIL.Image.Image.save`
方法来保存图片文件. 当你保存图片文件的时候, 文件名显得尤为重要.
除非你指定扩展名, 默认情况下是自动沿袭本地存储格式的.

把图片的格式转换为 JPEG
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    for infile in sys.argv[1:]:
        f, e = os.path.splitext(infile)
        outfile = f + ".jpg"
        if infile != outfile:
            try:
                Image.open(infile).save(outfile)
            except IOError:
                print("cannot convert", infile)

第二个参数支持 :py:meth:`~PIL.Image.Image.save` 方法来指定图片的扩展名.
如果使用了非标准的扩展名, 则必须加上第二个参数.

生成 JPEG 缩略图
^^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    size = (128, 128)

    for infile in sys.argv[1:]:
        outfile = os.path.splitext(infile)[0] + ".thumbnail"
        if infile != outfile:
            try:
                im = Image.open(infile)
                im.thumbnail(size)
                im.save(outfile, "JPEG")
            except IOError:
                print("cannot create thumbnail for", infile)

值得注意的是, 库默认情况下是不会解码光栅图片数据除非是必须的.
当你打开一个文件的时候, 文件的头部将被用来识别文件扩展名和大小等等属性,
但是剩下的数据不会马上被处理.

这也暗示了打开一个图片其实是一个很快的操作, 只关乎到文件大小和压缩方式.
以下是一个简单的识别图片文件的小脚本:

识别图片文件
^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import sys
    from PIL import Image

    for infile in sys.argv[1:]:
        try:
            with Image.open(infile) as im:
                print(infile, im.format, "%dx%d" % im.size, im.mode)
        except IOError:
            pass

裁剪, 粘贴 和 合成图片
------------------------------------

:py:class:`~PIL.Image.Image` 类包含了可以让你操作图片的方法.
当你想从图片截取一部分的时候, 直接用 :py:meth:`~PIL.Image.Image.crop` 方法.

从图像中拷贝出子矩形
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    box = (100, 100, 400, 400)
    region = im.crop(box)

这个图像区域由含有4个元素的元组组成, 这四个元素分别代表 (左, 上, 右, 下).
Python Imaging Library 使用(0, 0)来表示在左上角的情况.
另外值得注意的是, 这些坐标的单位是像素(px), 所以上面的例子实际上表示了 300x300 像素.

这个图像区域现在可以在某些情况下进行处理.

在原图像中处理子矩形
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    region = region.transpose(Image.ROTATE_180)
    im.paste(region, box)

当你修改原图像的时候, 图像区域的大小必须和原图像保持一致.
另外, 图像区域不能扩充到图像便捷之外. 尽管如此, 原图像和目标图像的模式不必保持一致.
如果不一致, 目标图像会在保存的时候自动进行转换, 详见 :ref:`color-transforms` .

接下来是扩展实例:

翻转图像
^^^^^^^^^^^^^^^^

::

    def roll(image, delta):
        "Roll an image sideways"

        xsize, ysize = image.size

        delta = delta % xsize
        if delta == 0: return image

        part1 = image.crop((0, 0, delta, ysize))
        part2 = image.crop((delta, 0, xsize, ysize))
        part1.load()
        part2.load()
        image.paste(part2, (0, 0, xsize-delta, ysize))
        image.paste(part1, (xsize-delta, 0, xsize, ysize))

        return image

Note that when pasting it back from the :py:meth:`~PIL.Image.Image.crop`
operation, :py:meth:`~PIL.Image.Image.load` is called first. This is because
cropping is a lazy operation. If :py:meth:`~PIL.Image.Image.load` was not
called, then the crop operation would not be performed until the images were
used in the paste commands. This would mean that ``part1`` would be cropped from
the version of ``image`` already modified by the first paste.

For more advanced tricks, the paste method can also take a transparency mask as
an optional argument. In this mask, the value 255 indicates that the pasted
image is opaque in that position (that is, the pasted image should be used as
is). The value 0 means that the pasted image is completely transparent. Values
in-between indicate different levels of transparency. For example, pasting an
RGBA image and also using it as the mask would paste the opaque portion
of the image but not its transparent background.

The Python Imaging Library also allows you to work with the individual bands of
an multi-band image, such as an RGB image. The split method creates a set of
new images, each containing one band from the original multi-band image. The
merge function takes a mode and a tuple of images, and combines them into a new
image. The following sample swaps the three bands of an RGB image:

Splitting and merging bands
^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    r, g, b = im.split()
    im = Image.merge("RGB", (b, g, r))

Note that for a single-band image, :py:meth:`~PIL.Image.Image.split` returns
the image itself. To work with individual color bands, you may want to convert
the image to “RGB” first.

Geometrical transforms
----------------------

The :py:class:`PIL.Image.Image` class contains methods to
:py:meth:`~PIL.Image.Image.resize` and :py:meth:`~PIL.Image.Image.rotate` an
image. The former takes a tuple giving the new size, the latter the angle in
degrees counter-clockwise.

Simple geometry transforms
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    out = im.resize((128, 128))
    out = im.rotate(45) # degrees counter-clockwise

To rotate the image in 90 degree steps, you can either use the
:py:meth:`~PIL.Image.Image.rotate` method or the
:py:meth:`~PIL.Image.Image.transpose` method. The latter can also be used to
flip an image around its horizontal or vertical axis.

Transposing an image
^^^^^^^^^^^^^^^^^^^^

::

    out = im.transpose(Image.FLIP_LEFT_RIGHT)
    out = im.transpose(Image.FLIP_TOP_BOTTOM)
    out = im.transpose(Image.ROTATE_90)
    out = im.transpose(Image.ROTATE_180)
    out = im.transpose(Image.ROTATE_270)

``transpose(ROTATE)`` operations can also be performed identically with
:py:meth:`~PIL.Image.Image.rotate` operations, provided the `expand` flag is
true, to provide for the same changes to the image's size.

A more general form of image transformations can be carried out via the
:py:meth:`~PIL.Image.Image.transform` method.

.. _color-transforms:

Color transforms
----------------

The Python Imaging Library allows you to convert images between different pixel
representations using the :py:meth:`~PIL.Image.Image.convert` method.

Converting between modes
^^^^^^^^^^^^^^^^^^^^^^^^

::

    im = Image.open("lena.ppm").convert("L")

The library supports transformations between each supported mode and the “L”
and “RGB” modes. To convert between other modes, you may have to use an
intermediate image (typically an “RGB” image).

Image enhancement
-----------------

The Python Imaging Library provides a number of methods and modules that can be
used to enhance images.

Filters
^^^^^^^

The :py:mod:`~PIL.ImageFilter` module contains a number of pre-defined
enhancement filters that can be used with the
:py:meth:`~PIL.Image.Image.filter` method.

Applying filters
~~~~~~~~~~~~~~~~

::

    from PIL import ImageFilter
    out = im.filter(ImageFilter.DETAIL)

Point Operations
^^^^^^^^^^^^^^^^

The :py:meth:`~PIL.Image.Image.point` method can be used to translate the pixel
values of an image (e.g. image contrast manipulation). In most cases, a
function object expecting one argument can be passed to this method. Each
pixel is processed according to that function:

Applying point transforms
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # multiply each pixel by 1.2
    out = im.point(lambda i: i * 1.2)

Using the above technique, you can quickly apply any simple expression to an
image. You can also combine the :py:meth:`~PIL.Image.Image.point` and
:py:meth:`~PIL.Image.Image.paste` methods to selectively modify an image:

Processing individual bands
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # split the image into individual bands
    source = im.split()

    R, G, B = 0, 1, 2

    # select regions where red is less than 100
    mask = source[R].point(lambda i: i < 100 and 255)

    # process the green band
    out = source[G].point(lambda i: i * 0.7)

    # paste the processed band back, but only where red was < 100
    source[G].paste(out, None, mask)

    # build a new multiband image
    im = Image.merge(im.mode, source)

Note the syntax used to create the mask::

    imout = im.point(lambda i: expression and 255)

Python only evaluates the portion of a logical expression as is necessary to
determine the outcome, and returns the last value examined as the result of the
expression. So if the expression above is false (0), Python does not look at
the second operand, and thus returns 0. Otherwise, it returns 255.

Enhancement
^^^^^^^^^^^

For more advanced image enhancement, you can use the classes in the
:py:mod:`~PIL.ImageEnhance` module. Once created from an image, an enhancement
object can be used to quickly try out different settings.

You can adjust contrast, brightness, color balance and sharpness in this way.

Enhancing images
~~~~~~~~~~~~~~~~

::

    from PIL import ImageEnhance

    enh = ImageEnhance.Contrast(im)
    enh.enhance(1.3).show("30% more contrast")

Image sequences
---------------

The Python Imaging Library contains some basic support for image sequences
(also called animation formats). Supported sequence formats include FLI/FLC,
GIF, and a few experimental formats. TIFF files can also contain more than one
frame.

When you open a sequence file, PIL automatically loads the first frame in the
sequence. You can use the seek and tell methods to move between different
frames:

Reading sequences
^^^^^^^^^^^^^^^^^

::

    from PIL import Image

    im = Image.open("animation.gif")
    im.seek(1) # skip to the second frame

    try:
        while 1:
            im.seek(im.tell()+1)
            # do something to im
    except EOFError:
        pass # end of sequence

As seen in this example, you’ll get an :py:exc:`EOFError` exception when the
sequence ends.

Note that most drivers in the current version of the library only allow you to
seek to the next frame (as in the above example). To rewind the file, you may
have to reopen it.

The following class lets you use the for-statement to loop over the sequence:

Using the ImageSequence Iterator class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import ImageSequence
    for frame in ImageSequence.Iterator(im):
        # ...do something to frame...


Postscript printing
-------------------

The Python Imaging Library includes functions to print images, text and
graphics on Postscript printers. Here’s a simple example:

Drawing Postscript
^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    from PIL import PSDraw

    im = Image.open("lena.ppm")
    title = "lena"
    box = (1*72, 2*72, 7*72, 10*72) # in points

    ps = PSDraw.PSDraw() # default is sys.stdout
    ps.begin_document(title)

    # draw the image (75 dpi)
    ps.image(box, im, 75)
    ps.rectangle(box)

    # draw title
    ps.setfont("HelveticaNarrow-Bold", 36)
    ps.text((3*72, 4*72), title)

    ps.end_document()

More on reading images
----------------------

As described earlier, the :py:func:`~PIL.Image.open` function of the
:py:mod:`~PIL.Image` module is used to open an image file. In most cases, you
simply pass it the filename as an argument::

    im = Image.open("lena.ppm")

If everything goes well, the result is an :py:class:`PIL.Image.Image` object.
Otherwise, an :exc:`IOError` exception is raised.

You can use a file-like object instead of the filename. The object must
implement :py:meth:`~file.read`, :py:meth:`~file.seek` and
:py:meth:`~file.tell` methods, and be opened in binary mode.

Reading from an open file
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    fp = open("lena.ppm", "rb")
    im = Image.open(fp)

To read an image from string data, use the :py:class:`~StringIO.StringIO`
class:

Reading from a string
^^^^^^^^^^^^^^^^^^^^^

::

    import StringIO

    im = Image.open(StringIO.StringIO(buffer))

Note that the library rewinds the file (using ``seek(0)``) before reading the
image header. In addition, seek will also be used when the image data is read
(by the load method). If the image file is embedded in a larger file, such as a
tar file, you can use the :py:class:`~PIL.ContainerIO` or
:py:class:`~PIL.TarIO` modules to access it.

Reading from a tar archive
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import TarIO

    fp = TarIO.TarIO("Imaging.tar", "Imaging/test/lena.ppm")
    im = Image.open(fp)

Controlling the decoder
-----------------------

Some decoders allow you to manipulate the image while reading it from a file.
This can often be used to speed up decoding when creating thumbnails (when
speed is usually more important than quality) and printing to a monochrome
laser printer (when only a greyscale version of the image is needed).

The :py:meth:`~PIL.Image.Image.draft` method manipulates an opened but not yet
loaded image so it as closely as possible matches the given mode and size. This
is done by reconfiguring the image decoder.

Reading in draft mode
^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    im = Image.open(file)
    print("original =", im.mode, im.size)

    im.draft("L", (100, 100))
    print("draft =", im.mode, im.size)

This prints something like::

    original = RGB (512, 512)
    draft = L (128, 128)

Note that the resulting image may not exactly match the requested mode and
size. To make sure that the image is not larger than the given size, use the
thumbnail method instead.
