title: jsphylosvg 学习
author: cacacai
date: 2019-01-21 21:11:49
tags:
---
jsPhyloSVG是一个开源的javascript库，专门用于渲染高度可扩展树结构。jsPhyloSVG可以渲染复杂的树，但提供了一种简单的方法。它利用了为系统发生树，phyloXML指定的最新XML模式定义。

首页官网http://www.jsphylosvg.com/
官方文档http://www.jsphylosvg.com/documentation.php
<!-- more -->

# 获取jsphylosvg.js
[下载地址](http://www.jsphylosvg.com/source/jsphylosvg-1.55.zip)  
http://www.jsphylosvg.com/source/jsphylosvg-1.55.zip

# 引入jsphylosvg.js
```
<html>
<head>				
	<script type="text/javascript" src="js/raphael/raphael-min.js" ></script> 
	<script type="text/javascript" src="js/jsphylosvg-min.js"></script> 				
</head>
<body>
	<div id="svgCanvas"> </div>
</body>
</html>			
```
# 绘制一个简单的树

```
 
<html>
<head>
	<script type="text/javascript" src="/js/raphael/raphael-min.js" ></script> 
	<script type="text/javascript" src="/js/jsphylosvg-min.js"></script> 
	
	<script type="text/javascript">
	window.onload = function(){
			var dataObject = { newick: '(((Espresso:2,(Milk Foam:2,Espresso Macchiato:5,((Steamed Milk:2,Cappuccino:2,(Whipped Cream:1,Chocolate Syrup:1,Cafe Mocha:3):5):5,Flat White:2):5):5):1,Coffee arabica:0.1,(Columbian:1.5,((Medium Roast:1,Viennese Roast:3,American Roast:5,Instant Coffee:9):2,Heavy Roast:0.1,French Roast:0.2,European Roast:1):5,Brazilian:0.1):1):1,Americano:10,Water:1);' };
			phylocanvas = new Smits.PhyloCanvas(
				dataObject,
				'svgCanvas', 
				500, 500
           //,'circular' 圆形，默认是树状
			);
	};
	</script>
</head>
<body>
	<div id="svgCanvas"> </div>
</body>
</html>			
```
效果如图
![upload successful](\images\pasted-5.png)
# 数据格式
## Newick 格式
这是一种类似与非二叉树的结构，叶节点的结构主要是``` 字符串[子树]:长度 ```
```
newick: '(((Espresso:2,(Milk Foam:2,Espresso Macchiato:5,((Steamed Milk:2,Cappuccino:2,(Whipped Cream:1,Chocolate Syrup:1,Cafe Mocha:3):5):5,Flat White:2):5):5):1,Coffee arabica:0.1,(Columbian:1.5,((Medium Roast:1,Viennese Roast:3,American Roast:5,Instant Coffee:9):2,Heavy Roast:0.1,French Roast:0.2,European Roast:1):5,Brazilian:0.1):1):1,Americano:10,Water:1);'
```
对应的形状结构为

![upload successful](\images\pasted-5.png)
这种数据格式比较简单直白，但是不好编写和阅读

##  phyloXML格式

![upload successful](\images\pasted-7.png)

![upload successful](\images\pasted-8.png)

利用xml的层级关系来表达树的层级，这样就可以比较清晰和方便的定义树的形状了，还有自定义样式。后面的官方例子都是基于这种数据格式
## NeXML 格式
基本不用，需要就看官方文档

# 应用视觉，交互和图表功能
在基于数据格式的前提下，给用户更多的自定义格式
## 更丰富的叶子节点内容信息
demo http://www.jsphylosvg.com/examples/3-load-interactive-tree.php

定义格式
```
<clade>
  <name>Espresso</name>
  <branch_length>2.0</branch_length>
  <annotation>
    <desc>Base of many coffees</desc>
    <uri>http://en.wikipedia.org/wiki/Espresso</uri>
  </annotation>
</clade>   
```

## 样式参数

您可以通过覆盖默认值直接在XML中设置图表样式。要覆盖任何默认设置（如下所示），只需添加一个<parameters>元素，其中包含您要覆盖的参数的名称。如果参数特定于某个图表类型，则还必须指定此参数，如下例所示。
```
  <!-- 重新定义圆形状态下，要覆盖内部圆形树外部的空间量 -->
  <render>
	<parameters>
		<circular>
			<bufferRadius>0.4</bufferRadius>
		</circular>
	</parameters>
</render>	
  ```

		

以下是一些可以覆盖的默认值（在render-parameters.js中找到）：

```
Rectangular : {
	bufferX			: 200, 			// Reduces the available canvas space for tree branches, allowing
									// for more space for the textual/charting components
	bufferY			: 40,
	bufferInnerLabels : 10, 		// Pixels
	bufferOuterLabels : 5, 			// Pixels
	minHeightBetweenLeaves : 10,  	// Should probably set pretty low, as clipping may occur if it needs to be implemented		
	
	alignPadding	: 0,			// Pixels to push the labels out by - this extension should be 
									// compensated by an increase in bufferX too	
	alignRight		: false
},

/* Circular Phylogram */
Circular : {
	bufferRadius 		: 0.33,		// Margins of Tree Circle
									// If > 1, it is in pixels
									// If < 1, it is a percentage of the full canvas size		
	bufferAngle 		: 20,		// controls split size in circle		
	initStartAngle 		: 160,		
	innerCircleRadius 	: 0,
	minHeightBetweenLeaves : 5,

	/* Labels */
	bufferInnerLabels : 2, 			// Pixels
	bufferOuterLabels : 5 			// Pixels
},
/* Charts */
binaryCharts : [],
barCharts : [],

	/* Binary Defaults */
	binaryChartBufferInner : 5, 
	binaryChartBufferSiblings : 0.01,
	binaryChartThickness : 15,
	binaryChartDisjointed : false,
		
	/* Bar Defaults */
	barChartBufferInner : 3,
	barChartHeight : 50,
	barChartWidth : 0.5,	// If > 1, it is in pixels
							// If < 1, it is a percentage of the node width 
```

# 更多
更多的文档参考
http://www.jsphylosvg.com/documentation.php