### 跨文档消息传递
`postMessage()`接收两个参数，一条消息和一个表示消息接收方来自哪个域的字符串
```js
var iframeWindow = document.getElementById('myframe').contentWindow;
iframeWindow.postMessage('A secret', 'http://www.wrox.com');
```
第二行代码表示向内嵌框架中发送一条消息，并指定框架中的文档来源于`http://www.wrox.com`,接收到消息后，可以使用`onmessage`处理程序中检测消息来源可以确保传入的消息来自已知的页面

### 原生拖放
拖放元素将会依次触发`dragstart`, `drag`, `dragend`三个事件
默认情况下浏览器不会在拖动期间改变被拖动元素的外观，但可以自己修改，不过大多数浏览器会为正被拖动的元素创建一个半透明的副本，这个副本始终跟随着光标移动
当某个元素被拖动到一个有效的放置目标上，下列事件依次发生`dragenter`, `dragover`, `dragdrop`
自定义放置目标需要重写`dragenter`和`dragover`事件的默认行为
`dataTransfer`对象，它是事件对象的一个属性，用于被拖动元素向放置目标传递字符串格式的数据
`dataTransfer`对象有两个方法`getData()`， `setData()`。
```js
// 设置和接受文本数据
event.dataTransfer.setData('text', 'some text');
var text = event.dataTransfer.getData('text');

// 设置和接受URL
event.dataTransfer.setData('URL', 'http://lznism.com');
var url = event.dataTransfer.getData('URL');
```

### dropEffect\effectAllowed
确定被拖动元素以及作为放置目标的元素能够接收什么操作。为此需要访问`dropEffect`和`effectAllowed`
其中`dropEffect`属性可以知道被拖动元素能够执行那种放置行为
- none
- move
- copy
- link
在把元素拖动到放置目标上时，以上每个值都会导致光标显示为不同符号
`dropEffect`属性只有搭配`effectAllowed`属性才有用，effectAllowed属性表示允许拖动元素的哪种dropEffect
假设你想允许用户吧文本框中的文本拖放到一个`<div>`中。首先必须将`dropEffect`和`effectAllowed`设置为`move`

### 可拖动
`HTML5`为所有的HTML元素规定了一个`draggable`属性，表示元素是否可以拖动
```html
<!-- 让图像不可拖动 -->
<img src="a.jpg" draggale="false" />
```

### 媒体元素
HTML5新增了视频和音频标签
```html
<video src="a.mpg" id="myVideo">video player not available</video>
<audio src="a.mp3" id="myAudeo">audio player not available</audio>
```
相关属性
- width/height 播放器的宽高
- poster 指定图像的URI，在加载视频内容期间显示一副图像
- controls 浏览器应该显示UI控件，以便用户直接操作媒体
- autoplay

相关事件
- abort 下载中断
- canplay 可以播放
- canplaythrough 播放可以继续

自定义媒体播放器
使用`audio`和`vedio`元素的play()和pause()方法，可以手工控制媒体文件的播放。组合使用属性事件和这两个方法

检查编码器的支持情况
有一个JS API能够检测浏览器是否支持某种格式的编码器，这两个媒体元素都有一个`canPlayType()`的方法，该方法接受一种格式/编码器字符串，返回`prototype`、`maybe`或`""`
```js
if(audio.canPlayType("audio/mpeg")){}
```

Audio类型
```js
var audio = new Audio("sound.mp3");
EventUtil.addHandler(audio, 'canplaythrough', function(e){
	audio.play();
});
```
创建了新的Audio实例即可开始下载指定的文件。下载完成后调用play()就可以播放音频

### 历史状态管理
HTML5通过更新`history`对象为管理历史状态提供了方便，通过`hashchange`事件可以知道URL的参数什么时候发生了变化，即什么时候该有所反应。为此需要使用`history.pushState()`方法，该方法可以接收三个参数：状态对象，新状态的标题和可选的相对URL
```js
history.pushState({name: "Nicholas"}, "Nicholas' page", "nicholas.html");
```
pushState()会创建新的历史状态，所以`后退键`也能使用了。按下后退按钮就会触发window对象的popstage事件。该事件对象有一个state属性，这个属性就包含着当初以第一个参数出阿尼递给pushState()状态对象
```js
EventUtil.addHandler(window, 'popstate', function(e){
	var state = event.state;
	if(state){
		processState(state);
	}
})
```
