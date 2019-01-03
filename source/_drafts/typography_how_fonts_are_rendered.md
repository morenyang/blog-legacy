---
title: "字体渲染 : 如何渲染一个文字"
tags:
---

## 如何渲染一个字体

### 字体引擎

在设计了一款字体之后，就需要想办法把它渲染出来。虽然字体设计时是矢量的，但输出设备终究还是按点阵的方式来展示。字体引擎做的就是把这些数学符号变成能在屏幕或者打印机上输出光栅图像。

字体引擎的工作步骤大致如下：

1. 将字体的主轮廓缩放到适当的大小；
2. 将轮廓按照字体的说明对其网格；
3. 网格拟合轮廓被扫描转换为适合光栅显示的位图图像。

<style>
._table_font_engine_works{
  border: 1px solid #ccc;
  width: 100%
}

._table_font_engine_works td{
  border-width: 1px;
  padding: 0 10px;
  border: 1px solid #ccc;
}

._table_font_engine_works img {
  height: auto;
}
</style>
<table cellspacing="2" cellpadding="0" class="_table_font_engine_works">
      <tbody><tr align="center" valign="middle">
        <td align="center">
          <p><font size="+2">master outline</font></p>
          <p><img src="/blog/images/190102/fig2-1-1.gif" alt="figure 10" align="absmiddle" width="130" height="140" naturalsizeflag="3"></p>
        </td>
        <td align="center">
          <p><font size="+2">1-scaled outline</font></p>
          <p><img src="/blog/images/190102/fig2-1-2.gif" alt="figure 10" align="absmiddle" width="217" height="273" naturalsizeflag="3"></p>
        </td>
      </tr>
      <tr align="center" valign="middle">
        <td align="center">
          <p><font size="+2">2-grid-fitted outline</font></p>
          <p><img src="/blog/images/190102/fig2-1-3.gif" alt="figure 10" align="absmiddle" width="217" height="273" naturalsizeflag="3"></p>
        </td>
        <td align="center">
          <p><font size="+2">3-raster image</font></p>
          <p><img src="/blog/images/190102/fig2-1-4.gif" alt="figure 10" align="absmiddle" width="217" height="273" naturalsizeflag="3"></p>
        </td>
      </tr>
    </tbody>
</table>

至于字体引擎内部的工作细节，例如如何对其网格、如何将点还原成轮廓、如何界定轮廓的哪一步分需要填充等，[文档](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM02/Chap2.html)中花了很大的篇幅来介绍，本文不多描述。这里有一个要说明的点：在光栅化的时候，会把在轮廓上和轮廓内的像素中心点填充，但是如果轮廓占用了某一个或多个像素却没有穿越中心点（如下图），甚至一些特殊情况，则会根据一套规则来判断哪些像素点需要填充。光栅化的规则如下文所示。

![figure_1](/blog/images/190102/fig2-9.gif)

> Rule 1: If a pixel's center falls within or on the glyph outline, that pixel is turned on and becomes part of the bitmap image of the glyph.
>

> Rule 2a: If a horizontal scan line connecting two adjacent pixel centers is intersected by both an on-transition contour and an off-transition contour, and neither of the two pixels was already turned on by rule 1, turn on the left-most pixel.
>

> Rule 2b: If a vertical scan line connecting two adjacent pixel centers is intersected by both an on-transition contour and an off-transition contour, and neither of the two pixels was already turned on by rule 1, turn on the bottom-most pixel.
>

> Rule 3a: If a horizontal scan line connecting two adjacent pixel centers is intersected by both an on-transition contour and an off-transition contour, neither of the pixels was already turned on by rule 1, and the two contours continue on to intersect other scan lines (this is not a 'stub'), turn on the left-most pixel.
>

> Rule 3b: If a vertical scan line connecting two adjacent pixel centers is intersected by both an on-transition contour and an off-transition contour, neither of the pixels was already turned on by rule 1, and the two contours continue on to intersect other scan lines (this is not a 'stub'),turn on the bottom-most pixel.
>

### 字体指导和Hinting

纵使字体引擎在光栅化时会根据相应规则对填充的像素进行处理，在一些情况下（比如小字或低分辨率的时候）字体仍然无法较好的显示。为了保证在低分辨率下的文字具有易读性，且在高分辨率下能更好的还原字形设计，减小轮廓形状和网格位置之间相互作用造成的机会效应，字体设计者需要对字体渲染进行指导。

上面这段话可能有些抽象，具体看一下字体指导要避免的是什么样的问题，你就会明白它做的是什么。

假设某个字的字形是一个圆，要把这个字显示在屏幕上，就需要把轮廓叠加光栅网格上，然后根据一系列公式计算哪些像素的中心位于轮廓内或轮廓上，并将他们填充，而其他的则什么也不做。这样做可能导致一些不完美的后果。如下图所示，如果只是简单地将那些像素中心位于轮廓内或轮廓上的点填充，那么得到的形状既不是左右对称，又不是上下对称的。另外，如果你稍微移动一下圆的位置，又有可能得到另外的结果。

![figure_2](/blog/images/190102/fig3-1.gif)

这一现象也容易影响字体的易读性。例如下图中所示，不受控制的光栅化可能会产生文字杆重 (stem weights) 不均、脱落、字符特征丢失、衬线不对称等问题。

![figure_3](/blog/images/190102/fig3-2-1.gif)![figure_3](/blog/images/190102/fig3-2-2.gif)![figure_3](/blog/images/190102/img00306.gif)
