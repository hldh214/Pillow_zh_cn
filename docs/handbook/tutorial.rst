教程
==============

使用 Image 类
--------------

在Python中最重要的图像处理库非 :py:class:`~PIL.Image.Image` 莫属,
以同样的名字定义模块, 你能多样的实例化这个类, 无论是从文件读取图片,
亦或者是处理其它图片, 或者随心所欲的绘制你所想到的图形.

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

值得注意的是, 当你使用 :py:meth:`~PIL.Image.Image.crop` 方法来修改图像文件的时候,
:py:meth:`~PIL.Image.Image.load` 方法会首先被调用.
这是由于修改是一个惰性操作. 如果 :py:meth:`~PIL.Image.Image.load` 未被调用,
那么在保存修改之前都不会执行修改这个操作.
这暗示着 ``part1`` 会在首次修改 ``image`` 的时候被修改.

至于更多黑魔法, paste方法也可以传入一个表示透明度的可选参数.
当你使用了这个黑魔法, 传入255这个值将会使图像变得不透明.
反之传入0则会使图像完全透明. 传入中间值则会使图片半透明.
例如, 修改一个 RGBA 图像并且使用透明度参数将会影响它的前景色透明度,
而并不会影响它的背景色透明度.

Python Imaging Library 同样允许你操作多波段的图片, 比如RGB图片.
split 方法会创建一个图片集合, 每一个表示了这个图片的一个波段.
merge 方法需要传入一个mode参数和一个图片的元组, 然后融合这个图像.
以下示例演示了如何分割三波段的 RGB 图片:

分割与合并波段
^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    r, g, b = im.split()
    im = Image.merge("RGB", (b, g, r))

值得注意的是单一波段的图片, :py:meth:`~PIL.Image.Image.split` 返回这个图像本身.
如果需要操作不同波段, 可能需要你先把图片转换成 RGB 格式.

几何变换
----------------------

:py:class:`PIL.Image.Image` 类包含了 :py:meth:`~PIL.Image.Image.resize`
和  :py:meth:`~PIL.Image.Image.rotate` 方法来操作图像.
前者需要传入一个表示新大小的元组, 而后者则需要传入旋转的角度.

简单的几何变换
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    out = im.resize((128, 128))
    out = im.rotate(45) # degrees counter-clockwise

要想以90度旋转图片, 你既可以使用 :py:meth:`~PIL.Image.Image.rotate` 方法,
也可以使用 :py:meth:`~PIL.Image.Image.transpose` 方法.
后者也能水平或者垂直翻转图像.

旋转图像
^^^^^^^^^^^^^^^^^^^^

::

    out = im.transpose(Image.FLIP_LEFT_RIGHT)
    out = im.transpose(Image.FLIP_TOP_BOTTOM)
    out = im.transpose(Image.ROTATE_90)
    out = im.transpose(Image.ROTATE_180)
    out = im.transpose(Image.ROTATE_270)

使用 :py:meth:`~PIL.Image.Image.rotate` 也能完成 ``transpose(ROTATE)`` 操作,
把 `expand` 参数设置为 True 来同时修改图片的尺寸.

至于修改图片方向的一般方法是使用 :py:meth:`~PIL.Image.Image.transform` 方法.

.. _color-transforms:

色彩转换
----------------

Python Imaging Library 允许你使用 :py:meth:`~PIL.Image.Image.convert` 方法,
以像素为单位修改图像.

模式转换
^^^^^^^^^^^^^^^^^^^^^^^^

::

    im = Image.open("lena.ppm").convert("L")

这个库支持 "L" 模式和 "RGB" 模式的互相转换. 要想转换到其它的模式,
可能需要使用一个中介模式, 比如 "RGB".

图像效果增强
-----------------

Python Imaging Library 提供了一些用来增强图像效果的方法和模块.

滤镜
^^^^^^^

:py:mod:`~PIL.ImageFilter` 模块内置一个预定义的图像效果增强的滤镜,
可用 :py:meth:`~PIL.Image.Image.filter` 方法来实现效果增强.

使用滤镜
~~~~~~~~~~~~~~~~

::

    from PIL import ImageFilter
    out = im.filter(ImageFilter.DETAIL)

浮点运算
^^^^^^^^^^^^^^^^

:py:meth:`~PIL.Image.Image.point` 方法用来转换图片的像素值, 例如图像的对比度.
绝大多数情况下, 一个函数对象需要一个参数传入这个方法.
每一个像素将会被这个函数处理:

使用浮点运算
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # multiply each pixel by 1.2
    out = im.point(lambda i: i * 1.2)

运行上述代码, 就能轻松处理一个图像.
另外你也可以结合 :py:meth:`~PIL.Image.Image.point` 方法和
:py:meth:`~PIL.Image.Image.paste` 方法来有的放矢的修改图像:

处理不同波段
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

请注意这里的语法::

    imout = im.point(lambda i: expression and 255)

Python 只会计算结果所需的逻辑表达式, 并返回最后一个表达式的计算结果.
所以, 如果存在某个表达式返回了 false 也就是0, Python 不会进行后续的计算并且返回0,
反之则返回255.

效果增强
^^^^^^^^^^^

更多图像增强效果, 都能在 :py:mod:`~PIL.ImageEnhance` 模块中找到.
一旦你实例化了一个图像, 效果增强对象就可以直接调用了.

你可以像这样修改对比度, 亮度, 色彩平衡度和锐度.

效果增强
~~~~~~~~~~~~~~~~

::

    from PIL import ImageEnhance

    enh = ImageEnhance.Contrast(im)
    enh.enhance(1.3).show("30% more contrast")

图像阵列
---------------

Python Imaging Library 支持一些对基本图像阵列. 其中包括 FLI/FLC, GIF,
和其它的一些格式. TIFF 文件则包含了多个帧.

当你试图打开一个图像阵列图片, PIL 会自动的加载这个阵列的首帧.
你可以使用一些方法来切换不同的帧:

读取阵列
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

如你所见, 在帧尾时会得到一个 :py:exc:`EOFError` 异常.

值得注意的是, 目前版本的库仅仅支持你顺序加载帧. 如果你想回头的话, 只能重新加载图像文件.

下列类允许你使用 for 语句来迭代这个序列:

使用图像序列迭代器
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import ImageSequence
    for frame in ImageSequence.Iterator(im):
        # ...do something to frame...


Postscript 打印
-------------------

Python Imaging Library 包含了一些用于输出图像的函数, 文字和在 Postscript 上的图像. 见下例:

绘制 Postscript
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

更多关于读取图片
----------------------

在上文中, :py:mod:`~PIL.Image` 模块里的 :py:func:`~PIL.Image.open` 函数用于打开图片文件.
但是在大多数情况中, 你可以优雅的打开它, 像这样::

    im = Image.open("lena.ppm")

如果没有报错, 返回值是 :py:class:`PIL.Image.Image` 对象.
反之则会抛出一个 :exc:`IOError` 异常.

你可以使用文件对象来代替文件名. 这个对象必须以 :py:meth:`~file.read`,
 :py:meth:`~file.seek` 和 :py:meth:`~file.tell` 方法, 并且以二进制方式打开.

从打开的文件中读取图片
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    fp = open("lena.ppm", "rb")
    im = Image.open(fp)

要想从字符串中读取文件, 使用 :py:class:`~StringIO.StringIO` 类来实现:

从字符串中读取图片
^^^^^^^^^^^^^^^^^^^^^

::

    import StringIO

    im = Image.open(StringIO.StringIO(buffer))

请注意, 使用库的时候会重置指针到文件开头. 另外, 在读取图片的时候也要用到文件指针.
如果图片文件被嵌入到了一个大文件, 例如 tar 文件, 你可以使用 :py:class:`~PIL.ContainerIO`
或者 :py:class:`~PIL.TarIO` 模块来处理它.

从tar文件中读取
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import TarIO

    fp = TarIO.TarIO("Imaging.tar", "Imaging/test/lena.ppm")
    im = Image.open(fp)

控制解码器
-----------------------

一些解码器允许你在读取文件的时候操作图片. 这个在创建缩略图的时候相当有用,
可以成倍的加快读取速度.

:py:meth:`~PIL.Image.Image.draft` 方法可以操作一个仅打开但是未加载的图片.
只需要你重新配置图片解码器即可完成.

使用模拟模式
^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    im = Image.open(file)
    print("original =", im.mode, im.size)

    im.draft("L", (100, 100))
    print("draft =", im.mode, im.size)

这将输出如下结果::

    original = RGB (512, 512)
    draft = L (128, 128)

值得注意的是, 结果图像可能不满足要求模式和大小. 要想确保图像和给定大小没有出入,
使用缩略图方法替代之.
