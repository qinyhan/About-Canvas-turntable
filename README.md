# About-Canvas-turntable
canvas转盘文档说明
# GB-canvas-turntable
----

## 简介

适用于移动端的Canvas绘制可配置的转盘抽奖
  

## 使用

### Browser
	
```html
	<link rel="stylesheet" href="css/GB-canvas-turntable.css">
	<script src="js/GB-canvas-turntable.js"></script>
```

### 普通

```js
	gbTurntable.init({
        id: 'turntable',
        config: function(callback){
            // 获取奖品信息
            // 奖项 text 属性不能为空，用于显示或抽中奖品提示
            // img 为奖品图片地址，如果不为空则转盘奖品按图片方式显示
            callback && callback([{
              text: '1元红包',
              img: 'images/redpacket.png'
            }, {
              text: '2元红包'
            }, {
              text: '3元红包'
            }, {
              text: '显示器',
              img: 'images/display.png'
            }, {
              text: '5元红宝'
            }, {
              text: '6元红宝'
            }])
        },
        getPrize: function(callback) {
            // 获取中奖信息
            var num = Math.random() * 6 >>> 0,   //奖品ID
                chances = num;  // 可抽奖次数
                callback && callback([num, chances]);   
        },
        gotBack: function(data) {
            alert('恭喜抽中' + data);
        }
    });
```







## about

演示网页排版来自： [https://github.com/sofish/typo.css](https://github.com/sofish/typo.css)       



## License

[MIT](./LICENSE) © 2016 [givebest](https://github.com/givebest)

 HTML5 Canvas绘制转盘抽奖
提示，以下是实现思路，文章未尾有完整项目仓库。
下面简单介绍了HTML5 Canvas的来历、浏览器兼容性，主要内容是如何实现转盘抽奖，最后的DEMO非常简陋，真实场景还会有很多需要考虑和改进的地方(比如：浏览器前缀)，这里只讲一步步实现的思路。

Demo
在线演示

HTML5 Canvas
Canvas元素最早是Apple在Safari1.3中引入的，后在HTML5中标准化。

浏览器支持
浏览器支持

查看详细浏览器支持

兼容旧版IE可使用:

1 . ExplorerCanvas Github, Google Code

Google工程师使用VML(Vector markup Language)技术模拟Canvas，不支持一些高级功能，如：渐变、阴影、剪切区域等。

2 . FlashCanvas

依赖Flash插件，增加了更多功能支持，有Free、Pro版。
ExploreCanvas、FlashCanvas Free、FlashCanvas Pro功能支持对比：
http://flashcanvas.net/docs/canvas-api

Canvas API
https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API

抽奖转盘
## 1. 绘制扇形HTML
	```js
		<canvas id="canvas" width="300" height="300">抱歉！浏览器不支持。</canvas>
		JS
		var num = 6;   // 奖品数量
		var canvas = document.getElementById('canvas');
		var btn = document.getElementById('btn');
		if(!canvas.getContext){
		    alert('抱歉！浏览器不支持。');
		    return;
		}
		// 获取绘图上下文
		var ctx = canvas.getContext('2d');
		for (var i = 1; i <= num; i++) {
		    // 保存当前状态
		    ctx.save();
		    // 开始一条新路径
		    ctx.beginPath();
		    // 位移到圆心，下面需要围绕圆心旋转
		    ctx.translate(150, 150);
		    // 从(0, 0)坐标开始定义一条新的子路径
		    ctx.moveTo(0, 0);
		    // 旋转弧度,需将角度转换为弧度,使用 degrees * Math.PI/180 公式进行计算。
		    ctx.rotate(360 / num * i * Math.PI/180);
		    // 绘制圆弧
		    ctx.arc(0, 0, 150, 0,  2 * Math.PI / num, false);
		    if (i % 2 == 0) {
			ctx.fillStyle = '#ffb820';
		    }else{
			ctx.fillStyle = '#ffcb3f';
		    }
		    // 填充扇形
		    ctx.fill();
		    // 绘制边框
		    ctx.lineWidth = 0.5;
		    ctx.strokeStyle = '#f48d24';
		    ctx.stroke();
		    // 恢复前一个状态
		    ctx.restore();
		}
	```
	
调用绘图上下文的save()方法可以保存坐标系当前的状态。然后，再调用restore()方法可以返回保存过的前一个状态。如果要保存坐标系的状态，必须在应用任何变换之前调用save()，这样再调用restore()才能把坐标系恢复到正常状态。而在多步操作绘制复杂图形时，往往都需要多次保存坐标系状态。这些状态就如同浏览器中的历史记录一样。每次调用restore()，坐标系就会恢复到前一个最近的状态。

参考：

rotate: http://www.w3school.com.cn/tags/canvas_rotate.asp
arc: https://developer.mozilla.org/zh-CN/docs/Web/API/ CanvasRenderingContext2D/arc
http://www.w3school.com.cn/tags/canvas_arc.asp

### 2. 动画CSS

	```js
		#canvas{
		    border: 1px solid #000;
		    -webkit-transition: all 6s ease;
		    transition: all 6s ease;
		}

		canvas.style.transform = 'rotate(1800deg)';
		3. 优化
		未获取中奖数据前预旋转，增强用户体验

		CSS
		/* 1. 点击抽奖，动画匀速执行linear */
		#canvas{
		    border: 1px solid #000;
		    -webkit-transition: all 6s linear;
		    transition: all 6s linear;
		}
		/* 2. 获取中奖数据，动画匀速执行并慢慢结束ease-out */
		#canvas{
		    border: 1px solid #000;
		    -webkit-transition: all 6s ease-out;
		    transition: all 6s ease-out;
		}
	```
参考：
贝塞尔曲线

工具
FabricJS
一个简单而强大的 JavaScript Canvas 库。
http://fabricjs.com

TwoJS
Two.js 是面向现代 Web 浏览器的一个2D绘图 API。
http://jonobr1.github.io/two.js/

其它实现方式
CSS实现大转盘
http://sbdccc.wx.bad.uctoo.com/?_a=reward&_u=index.christmas&r_uid=39

完整代码

	```js
		<!DOCTYPE html>
		<html lang="zh-cn">
		<head>
		<meta charset="UTF-8">
		<title>Drawing sector</title>
		<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
		<style>
		    #turntable{
			position: relative;
		    }
		    #canvas{
			border: 1px solid #000;
			-webkit-transform: all 6s ease;
			transition: all 6s ease;
		    }
		    #btn{
			position: absolute;
			left: 120px;
			top: 120px;
			width: 60px;
			height: 60px;
			border-radius: 50%;
			background-color: #fff;
			line-height: 60px;
			text-align: center;
		    }
		    #btn:after{
			position: absolute;
			display: block;
			content: '';
			left: 10px;
			top: -32px;
			width: 0;
			height: 0;
			overflow: hidden;
			border-width: 20px;
			border-style: solid;
			border-color: transparent;
			border-bottom-color: #fff; 
		    }
		</style>
		</head>
		<body>
		    <div id="turntable">
			<canvas id="canvas" width="300" height="300">抱歉！浏览器不支持。</canvas>
			<a id="btn" href="javascript:;">抽奖</a>
		    </div>
		    <script>
			window.addEventListener('load', function(){
			    var num = 6;   // 奖品数量
			    if (num % 2 !== 0){
				alert('请配置偶数奖项');
			    }
			    var canvas = document.getElementById('canvas');
			    var btn = document.getElementById('btn');
			    if(!canvas.getContext){
				alert('抱歉！浏览器不支持。');
				return;
			    }
			    // 获取绘图上下文
			    var ctx = canvas.getContext('2d');
			    for (var i = 0; i < num; i++) {
				// 保存当前状态
				ctx.save();
				// 开始一条新路径
				ctx.beginPath();
				// 位移到圆心，下面需要围绕圆心旋转
				ctx.translate(150, 150);
				// 从(0, 0)坐标开始定义一条新的子路径
				ctx.moveTo(0, 0);
				// 旋转弧度,需将角度转换为弧度,使用 degrees * Math.PI/180 公式进行计算。
				ctx.rotate((360 / num * i + 360 / num / 2) * Math.PI/180);
				// 绘制圆弧
				ctx.arc(0, 0, 150, 0,  2 * Math.PI / num, false);
				if (i % 2 == 0) {
				    ctx.fillStyle = '#ffb820';
				}else{
				    ctx.fillStyle = '#ffcb3f';
				}
				// 填充扇形
				ctx.fill();
				// 绘制边框
				ctx.lineWidth = 0.5;
				ctx.strokeStyle = '#f48d24';
				ctx.stroke();

				// 文字
				ctx.fillStyle = '#fff';
				ctx.font="16px sans-serif";
				ctx.fillText(i + 1, 100, 60);

				// 恢复前一个状态
				ctx.restore();
			    }

			    // 抽奖
			    btn.onclick = function(){
				canvas.style.transform = 'rotate(1800deg)'; 
			    }
			}, false);
		    </script>
		</body>
		</html>
	```
新项目：完整的Canvas转盘抽奖代码
https://github.com/givebest/GB-canvas-turntable

演示
http://blog.givebest.cn/GB-canvas-turntable.html

转载请注明出处：http://www.cnblogs.com/givebest/p/5296335.html
