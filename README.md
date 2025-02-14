# Live2D with Music Player
Hi，这是拷贝自鸦鸦的宠物播放器。

以下是原作者说明 PS ：只是修改了一些链接改为本项目

此版本极大地简化了代码逻辑。  

效果演示：[Live2D 宠物功能修改 | 音乐播放器+右键秘密通道](https://www.watlam.com/68.html)  

![maomi](https://github.com/watlam/live2d/assets/144750763/8608463a-99d9-417d-abb9-e947480354d5)

### 功能更新：
1. 白猫/黑猫支持随时间自动切换啦！
2. 右键秘密通道增加了极简模式，此模式下会去掉页面背景并启用灰度模式，上班时间访问博客不像在摸鱼啦！

![maomi](https://github.com/watlam/live2d/assets/144750763/b27fc087-68e8-454e-8d2c-497887fbc144)




### 主要特点：
1. 不再需要 API
2. 不再需要自定义 CSS（已集成到 waifu.css）
3. 不再需要修改任何 JS（所有配置参数已挪到页尾脚本）

现在，使用者可以专心定制说话内容和模型皮肤，而不再需要理解复杂的代码。

## 快速复现指南
### 方式一：直接引用CDN（或者复刻到自己的仓库再通过jsdelivr引用）
此时页尾脚本一共需要添加这些东西：
```
<!--宠物播放器-->
<script>const live2d_path = "https://cdn.jsdelivr.net/gh/SEYYl/live2d/live2d/";</script>
<meting-js server="tencent" type="playlist" id="8559460487" theme="#339981" fixed="true" preload="none" autoplay="false" loop="all" order="random" volume="0.3"></meting-js>
<script>
//主题未安装font awesome 需要引入；已有可以删除此行代码
<link rel="stylesheet" href="https://fastly.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css">
//封装异步加载资源的方法
function loadExternalResource(url, type) {
	return new Promise((resolve, reject) => {
		let tag;
		if (type === "css") {
			tag = document.createElement("link");
			tag.rel = "stylesheet";
			tag.href = url;
		}
		else if (type === "js") {
			tag = document.createElement("script");
			tag.src = url;
		}
		if (tag) {
			tag.onload = () => resolve(url);
			tag.onerror = () => reject(url);
			document.head.appendChild(tag);
		}
	});
}

if (screen.width >= 768) {
	Promise.all([
		loadExternalResource("https://cdn.jsdelivr.net/gh/watlam/live2d/live2d/waifu.min.css", "css"),
		loadExternalResource("https://cdn.jsdelivr.net/gh/watlam/live2d/live2d/live2d.min.js", "js"),
		loadExternalResource("https://cdn.jsdelivr.net/gh/watlam/live2d/live2d/waifu-tips.min.js", "js"),
		loadExternalResource("https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css", "css"),
		loadExternalResource("https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js", "js"),
	]).then(() => {
		loadExternalResource("https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js", "js");
	});
	ap = null;
	Object.defineProperty(document.querySelector('meting-js'), "aplayer", {
		set: function(aplayer) {
        		ap = aplayer;
        		ap_init();
        		initWidget();
		}
	});
}
</script>
```

### 方式二：放到自己服务器上
1. 下载整个 live2d 文件夹，通过宝塔面板上传到 `/www/wwwroot/wordpress/wp-content/uploads/`
2. 页尾脚本一共需要添加这些东西：（`live2d_path`换成自己的链接）
```
<!--宠物播放器-->
<script>const live2d_path = "https://www.watlam.com/wp-content/uploads/live2d/";</script>
<meting-js server="tencent" type="playlist" id="8559460487" theme="#339981" fixed="true" preload="none" autoplay="false" loop="all" order="random" volume="0.3"></meting-js>
<script>
//主题未安装font awesome 需要引入；已有可以删除此行代码
<link rel="stylesheet" href="https://fastly.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css">
//封装异步加载资源的方法
function loadExternalResource(url, type) {
	return new Promise((resolve, reject) => {
		let tag;
		if (type === "css") {
			tag = document.createElement("link");
			tag.rel = "stylesheet";
			tag.href = url;
		}
		else if (type === "js") {
			tag = document.createElement("script");
			tag.src = url;
		}
		if (tag) {
			tag.onload = () => resolve(url);
			tag.onerror = () => reject(url);
			document.head.appendChild(tag);
		}
	});
}

if (screen.width >= 768) {
	Promise.all([
		loadExternalResource(live2d_path + "waifu.css", "css"),
		loadExternalResource(live2d_path + "live2d.min.js", "js"),
		loadExternalResource(live2d_path + "waifu-tips.js", "js"),
		loadExternalResource("https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css", "css"),
		loadExternalResource("https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js", "js"),
	]).then(() => {
		loadExternalResource("https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js", "js");
	});
	ap = null;
	Object.defineProperty(document.querySelector('meting-js'), "aplayer", {
		set: function(aplayer) {
        		ap = aplayer;
        		ap_init();
        		initWidget();
		}
	});
}
</script>
```

## 初步修改
- 如需改为自己的歌单，请修改 `<meting-js>` 标签内的[参数](https://github.com/metowolf/MetingJS#option)。  
- 如需修改秘密通道或对话文本，请自定义 waifu-tips.json。 

## 文件功能说明（深度修改指南）
- model——模型文件直接作为子文件夹放于其中（可以自定义）  
- model_list.json——模型列表（可以自定义）  
- waifu-tips.json——对话文本（可以自定义）  
- live2d.min.js——Live2D动画核心文件，不需要修改  
- waifu-tips.js——按钮功能，一般不需要修改  
- waifu.css——外观样式，一般不需要修改  

## 相关项目
- [APlayer](https://github.com/DIYgod/APlayer)
- [MetingJS](https://github.com/metowolf/MetingJS)
- [Live2D Widget](https://github.com/stevenjoezhang/live2d-widget)
