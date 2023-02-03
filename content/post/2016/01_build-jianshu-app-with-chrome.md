---
title: 快速将网页版简书打造成具有个人特色的简书桌面应用
date: 2016-03-01T20:27:02
categories:
  - 工具
tags: [markdwon]
toc: true
---

> 个人真正意义上第一篇技术博文，从输入到输出，离不开基本的基础积累。

近日对谷歌扩展以及应用很感兴趣，于是研究了下官方文档，特写此文记录一下，若有错误，敬请指教，如需转载，请说明出处。

## 1.技术需求

怎么用`html css javascript`这些前端技术来编写一个桌面应用，说到这，不得不说谷歌浏览器这款伟大的产品，其自行开发的V8引擎大大提升了`javascript`在`chrome`中的执行效率，甚至可以将谷歌浏览器想象成一个操作系统，而`chrome app`则是运行在其上的应用。

`chrome app`开发十分迅速，是一个非常好玩的技术，下面就以网页版的简书为例子，快速将其打造成一个桌面应用，而且还是兼容的呢。

> 文档以及书籍参考：
> 官方文档：[chrome apps about_apps](https://developer.chrome.com/apps/about_apps)
> 参考书籍：[Chrome扩展及应用开发](http://www.ituring.com.cn/book/1421)

利用谷歌直接将一个网页打造成桌面应用实现起来可谓十分轻松，开发者只需要掌握`html css javascript`前端技术，再结合官方文档，基本上都能快速掌握。

所以，只要你有基本的`html css javascript`技术，就可以很快的开发出有自己特色的简述桌面应用。反之，请去百度之，很快就可以基本掌握。

对于本篇文章的目的：**快速将网页版简书打造成桌面应用**，只需要掌握[Webview Tag](https://developer.chrome.com/apps/tags/webview)，便可完成简书的桌面应用。

在进行代码层次的说明之前，先上一张完成后的效果图：

![2016-02-20 19:22:14 .png](https://images-1252557999.file.myqcloud.com/uPic/1240-20220520214607359.png)

## 2.应用说明
在编写应用之前，请容我先解释一下`chrome app`应该包含哪些文件，其由以下部分组成。
>- **manifest**文件将应用的一些信息提供给Chrome:这个应用是？它怎么运行？需要哪些额外的权限？
>- **background script**用来创建一个事件页面然后可靠地管理软件生命周期
>- 所有代码必须包含在`chrome app`的包内，其中包含`html css js `以及Native Client模块。
>- 所有**icons**和其他有利资源最好也包括在包里面
>- **说明：**若想深入了解，可以去看官方文档：[chrome apps first_app](https://developer.chrome.com/apps/first_app)

知道了这些后，我们就可以看一下代码的目录结构，进行了解，具体再一一说明：

![2016-03-05 23:05:43.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203818470.png)

## 2.代码说明
可以看到图中分别有`css/ images/ js/ `文件夹以及`main.html manifest.json `文件，不过最主要的是`manifest.json main.html background.js`这三个文件，下面的叙述也是围绕这三个文件来做讲解。

**2.1.manifest.json**
`manifest.json`文件的作用在上面的应用说明中已经解释得很清楚，其实不仅仅是`chrome app`，谷歌扩展也需要一个`json`格式的manifest，所以`manifest.json`文件很重要。
代码如下所示：
``` json
{
	"app":{
		"background":{
			"scripts":["js/background.js"]
		}
	},
	"manifest_version":2,
	"name":"简书",
	"version":"0.1.0",
	"description":"谷歌应用版的简书，对网页版本作出一些优化#__#,添加到桌面方便启动.",
	"icons": { 
		"16": "images/ico_16.png",
		"48": "images/ico_48.png",
		"128": "images/ico_128.png" 
	},
        //权限
	"permissions":[//这里需要简书网页域名的权限以及webview调用简书页面显示到新窗口
		"http://www.jianshu.com/*",
		"webview"
	]
}
```
上面字段意思表达地很清楚，我们来看看:
```
app              //Event Page会监听onLaunched事件，随即创建窗口，应用介绍有说这个作用，这里意思是app下面的background域会通过js/background.js创建窗口。
manifest_version //整数表示文件自身格式的版本号,按照这个写就好了
name             //应用名称
version          //版本号
其他都是某字段对应的意思，这里不一一解释
```
如果你想更加详细地了解，我不会说360全部都翻译了谷歌的官方文档，请移步[manifest详细说明](http://open.chrome.360.cn/extension_dev/manifest.html)。

**2.2.main.html**
定义好`manifest.json`文件之后，现在浏览器知道了我们的应用叫什么，怎么运行的，需要哪些额外的权限。

那么，应用启动后，应用会通过`Event Page`监听`onLaunched`事件，然后创建一个窗口，那么窗口显示什么界面呢？这个界面就是`main.html`文件。

这个界面主要看开发者的心情，想怎么写就怎么写，但是不要忘了引入`background.js`文件，其作用下面再说。

代码如下所示：
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>简书桌面版</title>
	<link rel="stylesheet" type="text/css" href="css/main.css">
	<script type="text/javascript" src="js/control.js"></script>
</head>
<body>
	<div id="title_bar">简书--找回文字的力量
		<a id="close"></a>
		<a id="minimize"></a>
	</div>
	<webview id="web_content"></webview>
</body>
</html>
```
`main.html`文件里面的代码结构十分简单，这个界面可以分为三个部分，标题、控制按钮（关闭以及最小化）、webview显示的主体内容部分，当该窗口显示之后，若想对窗口进行样式上的修改，可以加一个 `css`文件，我这里是这样定义的：
``` css
//添加边框
body{
	margin: 0;
	padding: 0;
	border: #EEE 1px solid;
}
//标题栏部分样式
#title_bar{
	-webkit-app-region:drag;	    //作用是让鼠标可以拖动窗口界面
	height: 26px;
	line-height: 26px;
	font-size: 15px;
	background-color: #EEE;
	padding: 0 10px;
	box-sizing: border-box;
}
//控制图标样式
#title_bar a{
	position: relative;
	-webkit-app-region:no-drag;    //让控制图标可以被点击
	display: inline-block;
	float: right;
	height: 14px;
	width: 14px;
	margin: 6px;
	border: gray 1px solid;
	box-sizing: border-box;
	border-radius: 7px;
}
#close:hover{
	background-color: #cf4646;
}
#minimize:hover{
	background-color: #46B6CF;
}
/**
 * show content
 * 这定义了webview调用简书网页页面后的宽高
 */
#web_content{
	width: 100%;
	height: 614px;
}
```
好了，窗口的样式大概写出来了，下面要做的就是怎么将这个页面作为窗口显示出来，这就要看`background.js`文件了。

**2.3.background.js**
简单来说，background.js会指定应用启动是创建的窗口，上代码：
``` javascript
//Event Page监听onLaunched事件
chrome.app.runtime.onLaunched.addListener(function(){
	var screenWidth = screen.availWidth;
	var screenHeight = screen.availHeight;
	var width = 1200;
	var height = 640;
	var main_window = chrome.app.window.get('main');
	if (main_window) {
		main_window.show();
	}else{
        //这里就创建了一个id是main的新窗口，窗口内容是main.html	
		chrome.app.window.create("main.html",{
			id:'main',
			bounds:{
				width:width,
				height:height,
				left: Math.round((screenWidth-width)/2),
				top:  Math.round((screenHeight-height)/2)
			},
			minHeight: height,
			minWidth:  width,
			maxHeight: height,
			maxWidth:  width,
			frame: 'none'//不显示标题栏目，因为我们自己有写标题样式，不用使用默认的。
		});
	}
});
```
通过注释就会明白这段代码的意思，现在，我们的应用就已经创建完成了，我们可以看看界面是什么样子了。

不过在这之前，我们先要将应用加载到谷歌浏览器中，请打开谷歌浏览器，地址栏输入`chrome://extensions/`，打开开发者模式，点击`加载已解压的扩展程序...`，最后加载你创建的应用包，加载成功后如下所示：

![jianshu.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203831020.png)

点击启动后，请看：

![2016-03-06 12:07:14.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203837839.png)

`main.html`很好地显示出来了，其中标题（*简书--找回文字的力量*），两个控制按钮，中间一大快空白部分（*空白部分是即将显示的主题内容*）都是我们刚才定义的。

现在我们就差最后一步就可以完成这个简书桌面应用了，就是利用[Webview Tag](https://developer.chrome.com/apps/tags/webview)调用简书网页页面，将其显示在`main.html`的`<webview id="web_content"></webview>`中，我将具体代码写在了`js/control.js`中，如下：
```javascript
//webwiew methods
window.onload = function(){
	var web_content = document.getElementById('web_content');
	web_content.src="http://www.jianshu.com/";//定义获取的网页页面
//在应用显示之前加载以下文件，但是应用加载出来后，在应用里面某些界面还需要脚本，所以下面又增加了一个方法。
	web_content.addContentScripts([  
  	{
	    name: 'jianshu',
	    matches: ['http://*.jianshu.com/*'],
	    css: { files: ['css/jianshu.css'] },
	    js: { files: ['js/jquery.1.11.3.min.js','js/jianshu.js'] },
	    run_at: 'document_start'
	}]);
//在每次页面加载后加入以下文件
	web_content.addEventListener('loadcommit',
	function(e) {
		web_content.executeScript({ file: "js/jquery.1.11.3.min.js" });
		web_content.executeScript({ file: "js/jianshu.js" });
	});
//控制按钮的方法，缩小以及关闭，还多写了一个最大化的方法，不过没有调用
	var current_window = chrome.app.window.current();

	document.getElementById('minimize').onclick = function(){
		current_window.minimize();
	}

	document.getElementById('close').onclick = function(){
		current_window.close();
	}

	document.getElementById('maximize').onclick = function(){
		current_window.isMaximized()?current_window.restore():current_window.maximize();
	}
}
```
完成后界面是这样的：

![2016-03-06 12:24:57.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203923544.png)

**2.4.增加自己的特色**
到了这一步，恭喜你，你已经可以自己打造一个桌面应用了，到此，我们的简书桌面应用已经可以运行，我们现在要做的就是收尾工作，既然都已经将网页版本的简书都放进了我们的webview标签之中，那么我们为何不写一些自己想要的样式呢？

在`control.js`文件中，我们分别引入了`js/jquery.1.11.3.min.js js/jianshu.js css/jianshu.css`文件，那么它们是干什么的呢，这些文件可以在简书页面加载时候一同加载进去，让我来演示一下就明白了。
比如说，在上面的图中左侧有个手机按钮来提示下载简书app，但是我已经下载了不想再看到，所以我可以写个js将其隐藏掉，将代码写在` js/jianshu.js `里，审查元素，知道其class是：`ad-selector`，所以可以这么写：

```javascript
$(document).ready(function(){
	$(".ad-selector").hide();  //将那个图标隐藏
	$(".search-form").removeAttr('target');//不要target属性
	$('a').removeAttr('target');
})
```
我们可以来看看效果：

![2016-03-06 12:25:48.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203930565.png)
怎么样，还不错吧，同样的，如果你对简书布局有什么想改动的地方，可以将`css`代码写在 `css/jianshu.css`中，好了，现在简书桌面应用已经打造完成了，只要将这个应用移动到你的桌面，就可以和使用其他应用一样使用了，而且兼容所有平台，当然前提是你得有谷歌浏览器。

---
## 3.总结
终于，我们将网页版简书打造成了桌面版，是不是非常方便，总结来看就是我们先定义  `manifest.json`文件，告诉浏览器我们编写的应用是什么，怎么启动什么的，然后再定义启动页面`main.html`,最后`background.js`将其作为窗口调用。

这样一个桌面应用就完成了，如果要进行修改，可以利用webview进行引入`css js`文件，就如同`js/control.js`引入的`js/jianshu.js css/jianshu.css`文件，就是这样。

---
## 4.源码
>github地址 [chrome_extension](https://github.com/howie6879/chrome_extension)
>谷歌商店 [下载地址](https://chrome.google.com/webstore/detail/%E7%AE%80%E4%B9%A6/phocpflpobocakpjkkpemklpnajbggbn?utm_source=chrome-ntp-icon&hl=zh-CN) 

有兴趣的可以试试，谢谢。

---
## 5.更新
针对简书新版进行了更新，支持全屏，更好的写作体验。

![jianshu.png](https://images-1252557999.file.myqcloud.com/uPic/1240-20211024203939019.png)