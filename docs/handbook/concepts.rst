基本概念
========

Python Imaging Library 可以处理光栅图像, 也就是矩形的像素.

.. _concept-bands:

频段
-----

一个图片允许包含一个或多个频段. Python Imaging Library 允许你在单个图像中存储多个频段.
包括原图像的尺寸和大小. 举个例子, 一个PNG图片可能包含 'R', 'G', 'B', 和 'A'
频段来表示红, 绿, 蓝和 alpha 透明值. 有许多对应频段的操作. 例如: histograms.
它常常用来处理不同像素的每个频段的值.

要想获得图像的频段的数量和名称, 使用 :py:meth:`~PIL.Image.Image.getbands` 方法.

.. _concept-modes:

模式
-----

图像的 ``mode`` 定义了这个图像每一个像素点的大小. 目前版本支持以下标准模式:

    * ``1`` (1-bit 像素点, 黑白, 一个像素点占用一个byte)
    * ``L`` (8-bit 像素点, 黑白)
    * ``P`` (8-bit 像素点, 使用调色板来映射其他模式)
    * ``RGB`` (3x8-bit 像素点, 真彩)
    * ``RGBA`` (4x8-bit 像素点, 带有透明标记的真彩)
    * ``CMYK`` (4x8-bit 像素点, 分色)
    * ``YCbCr`` (3x8-bit 像素点, 图像视频格式)

      * 值得注意的是这个属于 JPEG, 而并不是 ITU-R BT.2020, standard

    * ``LAB`` (3x8-bit 像素点, L*a*b 色彩空间)
    * ``HSV`` (3x8-bit 像素点, Hue, Saturation, Value 色彩空间)
    * ``I`` (32-bit 有符号整数像素)
    * ``F`` (32-bit 浮点像素)

PIL 同样支持一些不常用的模式, 包括 ``LA`` (L with alpha),
``RGBX`` (true color with padding) and ``RGBa`` (true color with
premultiplied alpha). 然而, PIL 不支持用户自定义模式;
如果你需要使用上述没有提到的模式, 则需要使用图像对象序列.

你可以通过 :py:attr:`~PIL.Image.Image.mode` 属性获取图像的模式.
这是一个字符串类型的值.

尺寸
----

你可以通过 :py:attr:`~PIL.Image.Image.size` 属性读取到图像的大小.
这是一个由两个元素组成的元组, 其中第一个元素代表长, 第二个则代表宽, 单位是像素.

坐标系
-----------------

Python Imaging Library 使用笛卡尔坐标系, 使用 (0,0) 表示左上角.
值得注意的是, 坐标点表示的是一个像素的左上角, 而表示像素的中央则是 (0.5,0.5).

坐标系通常使用含有两个元素的元组进行交互.
表示矩形则使用以左上角的坐标打头的包含四个元素的元组, 例如,
一个 800x600 像素可以表示为 (0, 0, 800, 600).

调色板
-------

(``P``) 模式使用调色板来为每一个像素指定实际的颜色.

摘要
----

你可以通过 :py:attr:`~PIL.Image.Image.info` 属性来获取图片的摘要信息.
这是一个字典对象.

在读取和写入图像文件的时候涉及到的信息取决于文件模式.
大多读取的时候会获得 :py:attr:`~PIL.Image.Image.info` 属性, 但是在保存的时候不会获得.

过滤器
-------

当你需要对图像进行几何操作的时候, Python Imaging Library 提供了一些不同的滤镜.

``NEAREST``
    Pick the nearest pixel from the input image. Ignore all other input pixels.

``BILINEAR``
    For resize calculate the output pixel value using linear interpolation
    on all pixels that may contribute to the output value.
    For other transformations linear interpolation over a 2x2 environment
    in the input image is used.

``BICUBIC``
    For resize calculate the output pixel value using cubic interpolation
    on all pixels that may contribute to the output value.
    For other transformations cubic interpolation over a 4x4 environment
    in the input image is used.

``LANCZOS``
    Calculate the output pixel value using a high-quality Lanczos filter (a
    truncated sinc) on all pixels that may contribute to the output value. In
    the current version of PIL, this filter can only be used with the resize
    and thumbnail methods.

    .. versionadded:: 1.1.3
