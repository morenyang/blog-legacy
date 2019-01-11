---
title: "字体渲染 : 如何渲染一个文字"
tags:
date: 2019-01-11
---

上一篇文章讲了TrueType字体是怎么构成的。但是这些抽象化的数学公式终究需要被具象化的表达出来。本文将简单介绍字体的渲染方式和渲染时会遇到的问题以及这些问题是如何解决的。

## 字体引擎

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

至于字体引擎内部的工作细节，例如如何对其网格、如何将点还原成轮廓、如何界定轮廓的哪一步分需要填充等，[文档](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM02/Chap2.html)中花了很大的篇幅来介绍，本文不多描述。这里有一个要说明的点：在光栅化的时候，会把在轮廓上和轮廓内的像素中心点填充，但是如果轮廓占用了某一个或多个像素却没有穿越中心点（如下图），甚至一些特殊情况，则会根据一套规则来判断哪些像素点需要填充。光栅化的规则如下文所示。(例如下图中的情况会填充靠左侧的像素点。)

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

## 修正

纵使字体引擎在光栅化时会根据相应规则对填充的像素进行处理，在一些情况下（比如小字或低分辨率的时候）字体仍然无法较好的显示。为了保证在低分辨率下的文字具有易读性，且在高分辨率下能更好的还原字形设计，减小轮廓形状和网格位置之间相互作用造成的机会效应，要对字体进行一些“修正”。

上面这段话可能有些抽象，具体看一下这个修正的过程要避免的是什么样的问题，你就会明白它做的是什么。

假设某个字的字形是一个圆，要把这个字显示在屏幕上，就需要把轮廓叠加光栅网格上，然后根据一系列公式计算哪些像素的中心位于轮廓内或轮廓上，并将他们填充，而其他的则什么也不做。这样做可能导致一些不完美的后果。如下图所示，如果只是简单地将那些像素中心位于轮廓内或轮廓上的点填充，那么得到的形状既不是左右对称，又不是上下对称的。另外，如果你稍微移动一下圆的位置，又有可能得到另外的结果。

![figure_2](/blog/images/190102/fig3-1.gif)

这一现象也容易影响字体的易读性。例如下图中所示，不受控制的光栅化可能会产生文字杆重 (stem weights) 不均、脱落、字符特征丢失、衬线不对称等问题。

![figure_3](/blog/images/190102/fig3-2-1.gif)![figure_3](/blog/images/190102/fig3-2-2.gif)![figure_3](/blog/images/190102/img00306.gif)

在某些较高分辨率的情况下，为了准确的还原字体的原始设计，也可能导致渲染出来的文字清晰但不优雅的问题。例如下图中的字母e在对顶端只有单独一个像素，并且曲线的锯齿状不那么美观。

![figure_4](/blog/images/190102/IF3.gif)

因此，TrueType会试图在光栅化之前对字形进行一些变形，使像素能够更准确的被填充来避免此类问题。如下图就是变形后的轮廓和光栅化的输出的结果。

![figure_5](/blog/images/190102/IF4.gif)
![figure_5](/blog/images/190102/IF5.gif)

实际上这个光栅器渲染“美化”的过程需要字体制作者对字体进行“指示”。也就是说，字体制作者要告诉引擎如何把文字渲染得更好。下面是指示时要注意的几个要点。

第一步是确定字体的用途。本质上来说是根据你想优化的输出设备确定每个em中网格单元的密度。通常来说，会针对72dpi以上的设备优化出屏幕大小可读的字体，这种情况下，字体每个em的单元数可以降到9。

修正时，重要的是保留字体的整体性和字体内各形状之间的关系。在这一步中，重要的是要确定一些关键尺寸，例如大写字母高度(cap height)，x高度(x height)和基线(baseline)。特别是在低分辨率的情况下，这些尺寸在每个字形中应该是一致的，否则会导致样式混乱。

接着是要确定需要保留哪些字符特征。在低分辨率时，可能需要牺牲设计的某些方面来使文字具有易读性。因此，这些字符特征的保留和舍弃应有优先顺序。一般来说，要保留的主要特性包括杆重、衬线、斜线等。

在网格拟合字形时，不仅要控制光栅化后要填充的像素，还应控制笔画之间、字形之间的间距(white space)。尤其是在使用屏幕显示文字时，控制间距是需要尤为注意的工作。如果不能很好的控制笔画之间的间距，则有可能导致字符在屏幕上看起来像黑色的斑点。

除此之外，还要控制前进宽度(advance width)。这一步对于确保文字之间的间距一致有很重要的作用。

为了获得最佳效果，字体设计师应该遵循一些指示规则。这个准则包括渲染结果的一致性，对齐，在大小尺寸时应忽略和保留那些细节等。

最后，还需对添加一些异常处理逻辑。另外，如果在某些特定大小下文字的渲染效果不好，则可以提供位图来代替（例如一些中文字体在小字的时候会使用点阵图形）。

至于在TrueType如何对字体指示进行声明，以及字体引擎具体是怎么处理这些指令的，本文不做介绍。如果有兴趣可以参考[文档](https://docs.microsoft.com/en-us/typography/truetype/hinting-tutorial/hinting-and-truetype-instructions)。

## 打印机的差别

实际上，由于渲染引擎的不同，不同的操作系统、软件实际渲染出来的文字即使是同一字体同一字号，也会大相径庭。下图是字母a在不用浏览器、操作系统下在16px下显示的效果。

![figure_6](/blog/images/190102/aaaa.png)

这里来聊一下我最常见的两种字体显示情况的差异—— Windows 和 Mac 系统上字体显示的差异。

一般来说，在同等分辨率下，如果都是用矢量字体，Windows系统中的文字显示效果一定是比Mac系统中显示的更清晰，特别是在小字情况下。

如果你把Windows系统上显示的问题截图并放大查看，会不难发现这些文字的笔画大多是非黑即白的。这是因为Windows的显示策略是优先保证文字的易读性，即使改变了字体的原有形状，也不惜使用很重的hinting。

而像苹果、Adobe这种常年混迹媒体和出版业的公司，更愿意强调屏幕显示的效果和印刷效果保持一致。因此Mac系统中的文字渲染时会跟忠于字体本身的设计，即使在屏幕上不能非常清晰的显示也无所谓。

关于操作系统对文字渲染逻辑的差别，有兴趣的可以看一看[这篇文章](https://blog.typekit.com/2010/10/15/type-rendering-operating-systems/)。

关于文字渲染分享，本文就分享到这里。其实把文字渲染出来这门学问还有很多很深层次的内容，这几篇文章也只是牵线的介绍一下。如果你想要了解更多，不妨翻看一下微软和苹果的文档，来了解这门学问的更多知识。

## 参考

- [Type rendering: operating systems](https://blog.typekit.com/2010/10/15/type-rendering-operating-systems/)
- [Instructing Fonts](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM03/Chap3.html#intro)
- [TrueType hinting](https://docs.microsoft.com/en-us/typography/truetype/hinting)
- [TrueType fundamentals](https://docs.microsoft.com/en-us/typography/opentype/spec/ttch01)
