@[toc](javascript结合css3制作手机菜单左滑特效（附演示地址和源码）)

# 一、前言

现在我们见过很多手机展示菜单的方式是整个页面向左滑动，然后右侧出现菜单，然后点击或者向右滑动左侧页面收起菜单。这种菜单用户体验比较好，现在一些针对手机端框架都包含了这种效果，例如jqm，但我们实际开发的时候往往只需要这一个效果，如果调用整个框架，总感觉有点浪费。而且有了之前使用jqm的不愉快经历，感觉不会再爱了-_-b

所以还是自己做一个把。

# 二、在线演示和GitHub源码
请用手机观看，效果更佳。
效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191015143155976.gif)
[-->在线演示地址](https://lujingtao.github.io/phone-menu-slide-using-javascript-css3/)
[-->GitHub源码](https://github.com/lujingtao/phone-menu-slide-using-javascript-css3)

# 三、原理说明
我这里只说大概思路，具体的有兴趣可以看看代码（js还是比较多的，还要和css结合）：

- 1、body里面包含2个同级层，A：网页具体内容，B：右侧菜单，B层绝对定位放到A层下面；
- 2、A层右上角放一个菜单按钮A-btn；
- 3、js控制：点击A-btn，A层向左滑动85%距离（把B层显示出来），同时js插入A层的遮罩层A-mark，放在A层上面；
- 4、js控制：A-mark添加点击和滑动事件控制A层向右滑动返回初始位置，然后隐藏A-mark。

思路就是这样，还要处理一些其它问题，例如A和B高度（包含高度变化时）匹配，A宽度超出屏幕等情况。

效果已经在ios和安卓上测试通过，可以投入使用。

# 四、核心代码
css：

```css
	/* rightMenuBtn */
	.rightMenuBtn{ width:32px; height:29px; border:1px solid #CF7F21; float:right; margin:12px 5px 0 0;  background:#dd8c28;
		-webkit-border-radius:3px; -moz-border-radius:3px; border-radius:3px; 
	}
	.rightMenuBtn i,
	.rightMenuBtn::before,
	.rightMenuBtn::after{  content:""; height:4px; width:24px; margin:4px auto 0 auto; display:block;  background:#fff; overflow:hidden; 
		-webkit-border-radius:3px; -moz-border-radius:3px; border-radius:3px; 
	}

	/* rightMenu */
	.rightMenu{ background:#222; }
	.rightMenu ul{ padding:3px }
	.rightMenu li{ height:40px; line-height:40px; padding:0 10px; color:#ccc; background:#333; border-bottom:1px solid #555   }
	.rightMenu li:nth-child(even){ background:#3d3d3d }	


	/* 右滑动菜单必要css */
	.rightMenu{  position:absolute; z-index:0; top:0; left:0; padding-left:15%; width:85%; min-height:100%;  }
	.wrap{ position:relative; left:0; top:0; width:100%;  z-index:1; background:#fff; min-height:100%;
		-webkit-transition:400ms;-o-transition:400ms;-moz-transition:400ms;transition:400ms;
	}
	.wrapMask{  position:absolute; left:0; top:0; width:100%; z-index:0; min-height:100%;  
		-webkit-transition:400ms;-o-transition:400ms;-moz-transition:400ms;transition:400ms;
	}
	.showRightMenu .wrap,
	.showRightMenu .wrapMask{
		overflow-x:hidden;
    	-webkit-transform: translate(-85%, 0);
        -moz-transform: translate(-85%, 0);
        -ms-transform: translate(-85%, 0);
        -o-transform: translate(-85%, 0);
        transform: translate(-85%, 0);
	}
	.showRightMenu .wrapMask{ display:block; z-index:2; }
	.showRightMenu .rightMenuBtn{  background:#CF7F21 }]	
```

js：

```csharp
	//左滑动菜单
	$(function(){

		//常量
		var body = $("body");
		var rm = $(".rightMenu"); //菜单
		var wrap =  $(".wrap"); //页面
		var rmBtn = $(".rightMenuBtn");//菜单按钮
		var wrapMask = $("<div class='wrapMask'></div>");//页面遮罩
		var bodyAddClassName = "showRightMenu";
		body.append( wrapMask );

		//触摸变量
		var isTouchPad = (/hp-tablet/gi).test(navigator.appVersion);
		var hasTouch = 'ontouchstart' in window && !isTouchPad;
		var touchStart = hasTouch ? 'touchstart' : 'mousedown';
		var touchMove = hasTouch ? 'touchmove' : 'mousemove';
		var touchEnd = hasTouch ? 'touchend' : 'mouseup';
		var startX = 0;
		var distX = 0;
		var slideDist = 40; //滑动距离

		//监听高度变化
		var setHeight = function(){
			if ( rm.outerHeight() < wrap.outerHeight() ){ rm.height(wrap.outerHeight());  }
			else{ wrap.height(rm.outerHeight()) }
			wrapMask.height(wrap.outerHeight());
		}
		setHeight();
		$(window).resize(setHeight);

		//点击事件
		rmBtn.click(function(){ body.toggleClass(bodyAddClassName);});
		wrapMask.click(function(){ body.removeClass(bodyAddClassName); });

		//触摸事件
		var start = function(e){
			var point = hasTouch ? e.touches[0] : e;
			distX = 0;
			startX =  point.pageX; 
			wrapMask[0].	addEventListener(touchMove, move,false);
			wrapMask[0].addEventListener(touchEnd, end ,false);
		}

		var move = function(e){
				var point = hasTouch ? e.touches[0] : e;
				e.preventDefault();
				distX = point.pageX-startX;
		}

		var end = function(e){
			if (distX>slideDist){ body.removeClass(bodyAddClassName); }
		}

		//添加“触摸开始”事件监听
		wrapMask[0].addEventListener(touchStart,start,false);

	})
```
