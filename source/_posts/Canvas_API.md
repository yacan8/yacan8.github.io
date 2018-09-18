---
title: Canvas API
date: 2018-09-18 19:58:59
tags:
- canvas
---

## 矩形

1. fillRect(x, y, width, height) 填充矩形
2. strokeRect(x, y, width, height) 绘制矩形边框
3. clearRect(x, y, width, height) 清除指定矩形区域，让清除部分完全透明。
4. rect(x, y, width, height) 绘制一个左上角坐标为（x,y），宽高为width以及height的矩形。

## 绘制路径

1. beginPath() 新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
2. closePath() 闭合路径，闭合路径之后图形命令又重新指向到上下文中。
3. stroke() 通过线条来绘制图形轮廓。
3. fill() 通过填充路径的内容区域生成实心图形。

当你调用fill()函数时，所有没有闭合的形状都会自动闭合，所以你不需要调用closePath()函数。但是调用stroke()时不会自动闭合。

## 移动触笔

1. moveTo(x, y) 将笔触移动到指定的坐标x以及y上。

当canvas初始化或者beginPath()调用后，你通常会使用moveTo()函数设置起点。我们也能够使用moveTo()绘制一些不连续的路径。

## 线

1. lineTo(x, y) 绘制一条从当前位置到指定x以及y位置的直线。

开始点也可以通过moveTo()函数改变。

## 圆弧

1. arc(x, y, radius, startAngle, endAngle, anticlockwise) 画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。
2. arcTo(x1, y1, x2, y2, radius) 根据给定的控制点和半径画一段圆弧，再以直线连接两个控制点。

arc()函数中表示角的单位是弧度，不是角度。角度与弧度的js表达式: 弧度=(Math.PI/180)*角度。

## 二次贝塞尔曲线及三次贝塞尔曲线

1. quadraticCurveTo(cp1x, cp1y, x, y) 绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
2. bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y) 绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。

## 色彩

1. fillStyle = color 设置图形的填充颜色。
2. strokeStyle = color 设置图形轮廓的颜色。

支持使用 rgba 形式。

## 透明度

1. globalAlpha = transparencyValue 这个属性影响到 canvas 里所有图形的透明度，有效的值范围是 0.0 （完全透明）到 1.0（完全不透明），默认是 1.0。

globalAlpha 属性在需要绘制大量拥有相同透明度的图形时候相当高效。相反，如果对单个元素添加透明度，推荐使用 rgba 形式。

### 线型

1. lineWidth = value 设置线条宽度。
2. lineCap = type 设置线条末端样式。
选项：
butt 线段末端以方形结束。
round 线段末端以圆形结束。
square 线段末端以方形结束，但是增加了一个宽度和线段相同，高度是线段厚度一半的矩形区域。
3. lineJoin = type 设定线条与线条间接合处的样式。
选项：
round 通过填充一个额外的，圆心在相连部分末端的扇形，绘制拐角的形状。 圆角的半径是线段的宽度。
bevel 在相连部分的末端填充一个额外的以三角形为底的区域， 每个部分都有各自独立的矩形拐角。
miter 通过延伸相连部分的外边缘，使其相交于一点，形成一个额外的菱形区域。这个设置可以通过 miterLimit 属性看到效果。
4. miterLimit = value 限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。
5. getLineDash() 返回一个包含当前虚线样式，长度为非负偶数的数组。
6. setLineDash(segments) 设置当前虚线样式。例：ctx.setLineDash([4, 16]);
7. lineDashOffset = value 设置虚线样式的起始偏移量。

## 渐变

1. createLinearGradient()方法创建一个沿参数坐标指定的直线的渐变。这个方法返回 CanvasGradient。
2. createRadialGradient() 是 Canvas 2D API 根据参数确定两个圆的坐标，绘制放射性渐变的方法。这个方法返回 CanvasGradient。

3. gradient.addColorStop(position, color) addColorStop 方法接受 2 个参数，position 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。例如，0.5 表示颜色会出现在正中间。color 参数必须是一个有效的 CSS 颜色值（如 #FFF， rgba(0,0,0,1)，等等）。

## 图案样式

1. createPattern(image, type) Image 可以是一个 Image 对象的引用，或者另一个 canvas 对象。Type 必须是下面的字符串值之一：repeat，repeat-x，repeat-y 和 no-repeat。

如：
```js
var img = new Image();
img.src = 'someimage.png';
var ptrn = ctx.createPattern(img,'repeat');
```

与 drawImage 有点不同，你需要确认 image 对象已经装载完毕，否则图案可能效果不对的。

## 阴影

1. shadowOffsetX = float 设定阴影在 X 轴的延伸距离。
2. shadowOffsetY = float 设定阴影在 Y 轴的延伸距离。
3. shadowBlur = float 用于设定阴影的模糊程度，其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0。
4. shadowColor = color 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色。

## 绘制文本

1. fillText(text, x, y [, maxWidth]) 在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的.
2. strokeText(text, x, y [, maxWidth]) 在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的.
3. font = value 这个字符串使用和 CSS font 属性相同的语法. 默认的字体是 10px sans-serif。
4. textAlign = value 文本对齐选项. 可选的值包括：start, end, left, right or center. 默认值是 start。
5. textBaseline = value 基线对齐选项. 可选的值包括：top, hanging, middle, alphabetic, ideographic, bottom。默认值是 alphabetic。
6. direction = value 文本方向。可能的值包括：ltr, rtl, inherit。默认值是 inherit。
7. measureText() 将返回一个 TextMetrics对象的宽度、所在像素，这些体现文本特性的属性。

## 