---
title: ECharts中的自定义拖拽功能
date: 2020-03-03 16:04:40
tags: echarts
---
在使用 ECharts 的时候，通常是使用折线图（line）、饼图（pie）、柱状图（bar）、关系图（graph）等这些 series 以展示数据和数据关系。如果要基于这些 series 增加一些拖拽的交互，就需要涌动 ECharts 的 [graphic](https://echarts.apache.org/zh/option.html#graphic) 组件。  
graphic 提供了绘制原生图形元素的功能，包括：image, text, circle, sector, ring, polygon, polyline, rect, line, bezierCurve, arc, group，基本都是 [zrender](http://ecomfe.github.io/zrender/) 支持的图形。  
通常在原有的 series 上实现拖拽功能，有以下步骤：   
- 在需要交互的区域使用 graphic 绘制一个不可见的图形；
- 监听 graphic 的拖拽事件，同步更新 series 的 data 坐标；
- 监听 series 和整个 ECharts 的变化，同步 graphic 元素的坐标

第一步：  
通常是将 graphic 覆盖到 series 的 data 位置，所以需要获取到每个 data 的坐标，data 的坐标有两个，一个是数据意义上的坐标，一个是图形上的坐标。绘制 graphic 元素需要的是图形坐标，所以需要使用 [convertToPixel](https://echarts.apache.org/zh/api.html#echartsInstance.convertToPixel) 根据 data 的数据坐标转换成图形坐标确定 graphic 元素的 position。  
ps：在绘制 graphic 元素的时候，避免使用点坐标去控制位置，直接使用 position 属性去控制位置。比如要绘制一个 line，可以设置起点坐标和终点坐标来确定 line 的位置，但建议都从[0, 0] 开始绘制，然后设置 position 去确定位置，否则在后面同步 graphic 的坐标到 data 坐标的时候会有问题。  
第二步：  
在 graphic 元素的 ondrag 方法中，根据获取到图形坐标，使用 [convertFromPixel](https://echarts.apache.org/zh/api.html#echartsInstance.convertFromPixel) 将图形坐标转为数据坐标信息，重新 setOption 更新 ECharts 信息。  
第三步：  
对于 EChart 的resize 或者 dataZoom 等会引起表格变化的事件，也要同步更新 graphic 元素的坐标，所以需要监听相关事件，在事件回调中，重新绘制 graphic 元素。  

基本上都是这个套路，细节问题就得看具体情况了。  
