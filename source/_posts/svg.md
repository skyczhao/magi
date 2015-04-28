title: "svg"
date: 2015-04-28 15:08:40
tags: 图形
---

SVG入门
-----

### 数字化图片的两种方案
* 其一为位图，也被称为光栅图。
    即是以自然的光学的眼光将图片看成在平面上密集排布的点的集合。每个点发出的光有独立的频率和强度，反映在视觉上，就是颜色和亮度。这些信息有不同的编码方案，在互联网上最常见的就是 RGB。根据需要，编码后的信息可以有不同的位 (bit) 数——位深。位数越高，颜色越清晰，对比度越高；占用的空间也越大。另一项决定位图的精细度的是其中点的数量。一个位图文件就是所有构成其的点的数据的集合，它的大小自然就等于点数乘以位深。位图格式是一个庞大的家族，包括常见的 JPEG/JPG, GIF, TIFF, PNG, BMP。
* 第二种方案为矢量图。
    它用抽象的视角看待图形，记录其中展示的模式而不是各个点的原始数据。它将图片看成各个“对象”的组合，用曲线记录对象的轮廓，用某种颜色的模式描述对象内部的图案（如用梯度描述渐变色）。比如一张留影，被看成各个人物和背景中各种景物的组合。这种更高级的视角，正是人类看世界时在意识里的反映。矢量图格式有 CGM, SVG, AI (Adobe Illustrator), CDR (CorelDRAW), PDF, SWF, VML 等等。

### SVG
这种技术来自XML家族，叫做 SVG(Scalabe Vetor Graphics) - 可缩放矢量图。作为一种通用的数据格式，XML 自诞生之日起，就不断表现出表达一切可表达之物的抱负，不仅要接纳新出现的各种信息，还要接收历史上以其他各种形式存储的数据。
- 2001-9SVG 1.0 成为 W3C 推荐。
- 2003-1SVG 1.1 成为 W3C 推荐。并演化出 SVG Tiny，SVG Basic 和 SVG Full 不同级别的细则。
- SVG 1.2 在之后的几年中一直处于工作草稿（W3C Working Draft）的状态，现已确定会被 SVG 2.0 取代。
- SVG 2.0 将会完全重写 SVG 1.2，以加入更多诸如 CSS，HTML5 的新特性。

### 简单例子
* 代码
```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="200px" height="200px" > 
    <circle cx="100" cy="100" r="40" fill="red"/> 
</svg>
```
* 怎么看？
    0. 直接粘贴到网页源码里面，打开即可，目前大部分浏览器已经支持
    1. `<img src="sun.svg" alt="">`

> 如果仅仅是将 SVG 作为图片引用，则只发挥了它的静态功能。
> 我们更感兴趣的是应用它的动态功能。
> SVG 的动态功能包括两个方面。一为动画，二为支持脚本编程。

### 动画
```
<svg xmlns="http://www.w3.org/2000/svg"> 
    <polygon points="50,100  100,100  75,50" stroke="#660000" fill="#cc3333"> 
        <animateTransform 
        attributeName="transform"
        begin="0s"
        dur="10s"
        type="rotate"
        from="0 0 0"
        to="360 60 60"
        repeatCount="indefinite" 
        /> 
    </polygon> 
</svg>
```
polygon元素指定画一个多变形，这里给定了三个顶点，所以是一个三角形。将上面的代码保存成一个 SVG 文件，在一个页面中引用，如果您的浏览器支持 SIML，屏幕上会显示一个不断旋转的红色三角形；如果您的浏览器只支持 SVG，将看到一个静止的红色三角形。

### 脚本编程
贝塞尔曲线

### 参考
* [JavaScript 的新领域 - 动态图片处理（SVG）](http://www.ibm.com/developerworks/cn/web/1107_pangjun_svgcanvas1/index.html)