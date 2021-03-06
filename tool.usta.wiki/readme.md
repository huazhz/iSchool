iTool 说明文档
===============
[TOC]

**注意：本文不讲解php、css、js等语言的语法，不讲解laravel框架的用法，不讲解代码运行的机制，只是讲解网站的架构、功能和设计等。作者认为这些代码你都是看得懂和会写的。**

## 一、iTool功能图和页面组成结构
手稿
![手稿][1]

iTool功能图
![iTool功能图][2]

iTool页面组成结构
![iTool页面组成结构][3]

## 二、从页面视图说开去
可能一般应该从路由开始说起，然后到控制器，然后到视图，这是这个网站运行的顺序。但如果这样说的话，显然不够形象，难于理解。

那么我们从每一个页面来说，或者从每一个部分来说，相信你能很快上手，如果你依然很难与理解这个网站的架构，那么好好去看看 [Laravel文档](http://laravelacademy.org/laravel-docs-5_2) 吧。

或许你应该先用Laravel开发一个网站后，这样会更容易理解这种模式。

### 2.1、头部
ok，看了上面的图，如果你经验丰富，肯定已经一眼看穿，“哦，原来这个网站这么简单”，那么下面的东西你基本可以跳过了。如果你只是个小白，好好学习，天天向上。

第一，我们从页面的最开始，头部开始说

```html
<meta charset="UTF-8" />
<title>{!! htmlspecialchars_decode($config['title']) !!} </title>
<meta content="IE=edge,chrome=1" http-equiv="X-UA-Compatible" />
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
<meta name="Keywords" content="{!! htmlspecialchars_decode($config['keyword']) !!}" />
<meta name="Description" content="{!! htmlspecialchars_decode($config['des']) !!}" />
<meta name="author" content="jtahstu" />
<link rel="icon" href="{!! htmlspecialchars_decode($config['icon']) !!}" />
<link href="http://apps.bdimg.com/libs/bootstrap/3.3.4/css/bootstrap.min.css" rel="stylesheet" />
<link rel="stylesheet" type="text/css" href="{{asset('public/css/header.css')}}" />
<link rel="stylesheet" type="text/css" href="{{asset('public/css/tool.css')}}" />
<script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="{{asset('public/js/tool.js')}}"></script>
{!! htmlspecialchars_decode($config['headAddCode']) !!}
{{--注意这里只要有html标签的，请一定要使用上面的写法，不要用{{}}或不解码，原因嘛，你懂的. --}}
```

上面这段就会生成下面的页面代码

```html
<title>iTool - 代码编辑神器 </title>
<meta content="IE=edge,chrome=1" http-equiv="X-UA-Compatible" />
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
<meta name="Keywords" content="itool，在线代码编辑器，在线运行，在线编译，ischool，jtahstu,666啊" />
<meta name="Description" content="这么叼？我都不知道呢。" />
<meta name="author" content="jtahstu" />
<link rel="icon" href="http://cdn.jtahstu.com/editor.ico" />
<link href="http://apps.bdimg.com/libs/bootstrap/3.3.4/css/bootstrap.min.css" rel="stylesheet" />
<link rel="stylesheet" type="text/css" href="http://tool.usta.wiki/public/css/header.css" />
<link rel="stylesheet" type="text/css" href="http://tool.usta.wiki/public/css/tool.css" />
<script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="http://tool.usta.wiki/public/js/tool.js"></script>
```

采用Blade模板引擎的语法，生成链接和载入变量值

**注意：这里配置里包含有html或js代码的，显示时需要特殊字符解码**

因为在配置提交时，会进行html特殊字符编码，然后保存到数据库中，以防止一些sql注入攻击，所以在从数据库中拿出来时需要解码,这个**请参考2.8、后台管理的代码说明**

需要什么：$config，即网站的配置数组

从哪来：控制器返回视图时带来的，也就是从数据库中读出来的

干什么：当然是显示喽，视图不就是呈现的嘛

### 2.2、导航栏

#### 2.2.1、电脑浏览器显示的导航栏
该导航栏使用的是网上仿阿里云前下拉模式导航栏，目前阿里云导航栏已改版，效果看上去还是过得去的。

需要什么：分页对象，即`$shareList= DB::table('tool_share')->paginate($paginateCount)`

从哪来：控制器以参数形式传入

干什么：以判断导航栏需要显示几个第多少多少页。

#### 2.2.2、移动端显示的导航栏
该导航栏使用的是Bootstrap的原生导航栏，由于其框架的适应性，在移动端显示体验还是可以的。

### 2.3、脚部
脚部也是有两种的，移动端访问就只有脚部声明，显示会相当简洁。

```php
<footer id="footer">
	<div class="container" id="footer-main">
		<div class="col-md-4">
			……
		</div>
		<div class="col-md-4">
			<div id="footer-3" class="layer">
				<h2>社交账号</h2>
				<img src="{{asset('public/img/qq.jpg')}}"/>
				<img src="{{asset('public/img/weixin.jpg')}}"/>
				<h2><a href="{{URL::to('/login')}}" target="_blank">
					su - root
				</a></h2>
			</div>
		</div>
	</div>
	<div class="col-md-12 center" id="footer-state">
		<p>
			iTool &copy;
			<?php echo date('Y'); ?>
			. {!! htmlspecialchars_decode($config['footCopy']) !!} 
		</p>
	</div>
</footer>
<script src="{{asset('public/js/layer.js')}}"></script>
<script>
	$(function() {
		layer.ready(function() {
			//使用相册
			layer.photos({
				photos: '.layer'
			});
		});
	});
</script>
<!--统计代码-->
{!! htmlspecialchars_decode($config['cnzz']) !!}

<?php
    if(isset($_GET['user'])||isset($_GET['page']))
		$jumpLink=Request::getRequestUri().'&m=m';
	else
		$jumpLink=Request::getRequestUri().'?m=m';
?>
<script src="http://siteapp.baidu.com/static/webappservice/uaredirect.js" type="text/javascript"></script>
<script type="text/javascript">uaredirect("{!! $jumpLink !!}");</script>
{{-- 判断移动端访问 --}}
```

#### 2.3.1、脚部分为三个部分

 - 三列并排显示的网站说明和社交账号等信息
 - 居中显示的脚部申明
 - 隐藏的统计代码
 
#### 2.3.2、脚部客户端类型验证

```php
<?php
    if(isset($_GET['user'])||isset($_GET['page']))
		$jumpLink=Request::getRequestUri().'&m=m';
	else
		$jumpLink=Request::getRequestUri().'?m=m';
?>
<script src="http://siteapp.baidu.com/static/webappservice/uaredirect.js" type="text/javascript"></script>
<script type="text/javascript">uaredirect("{!! $jumpLink !!}");</script>
{{-- 判断移动端访问 --}}
```

由于js加载应该放在网页的最后，又由于每个页面都要判断，所以验证就放在了脚部里。

实际上是放头部`Request::getRequestUri()`会运行不正常，不用太在意，放这里是对的。

**js实现客户端的类型判断，如果是移动端则跳转到当前链接并添加标记m=m**，说明是移动端访问，此标记用与页面显示时到底是加载移动端的头部和脚部，还是加载正常的浏览器头部和脚部。

由于管理后台和分页时链接中已经有变量了，所以需要判断一下，一个是？一个是&

**注意：在一开始访问网站的页面时是不带m=m标记的，验证如果是移动端访问，则会刷新页面，也就是说，移动端访问会加载两次页面。**

页面中的判断代码
```php
@if(isset($_GET['m']))
    @include('Mobile.header')
@else
	@include('Compile.header')
@endif

@if(isset($_GET['m']))
	@include('Mobile.footer')
@else
    @include('Compile.footer')
@endif
```

其中，脚部使用了

- layer.js弹出层显示QQ和微信的二维码
```
layer.ready(function() {
	//使用相册
	layer.photos({
		photos: '.layer'
	});
});
```
- js计算今天距离网站建立时天数差，代码在tool.js中
- 使用count.knowsky.com网站提供的计数器来计算有多少人此访问

后两个功能是先写好的，调用是在配置项中脚部的站点声明中。

### 2.4、首页
前面介绍了头部、导航栏和脚部，你会发现每个页面都有这三个部分，在结构图里也能轻易的看出来，每个页面不同的仅仅是中间的那一部分。

#### 2.4.1、图标
首页的中间部分是网上借鉴的，改了文字和图标，图标采用的是阿里矢量图标库[www.iconfont.cn](http://www.iconfont.cn/)里的图标

**这里可不是使用的图片，有好多人都问我你这用的图片吧，this is 矢量图**

CDN加载css文件
```
<link rel="stylesheet" type="text/css" href="//at.alicdn.com/t/font_1475409244_4673653.css" />
```
代码中引用
```
<i class="icon iconfont">&#xe609;</i>
```
就是这么简单，这个图标的写法有好几种，自己去阿里矢量图标库看看吧

目前这个项目的所有图标有
![iTool首页图标][4]

如果你也需要，直接加载css，然后调用即可，简单、方便、快速

#### 2.4.2、网页弹幕
网页弹幕使用的是barrager.js插件，Github地址：[https://github.com/yaseng/jquery.barrager.js](https://github.com/yaseng/jquery.barrager.js)

或者参考这篇文章和演示 [jquery.barrager.js专业的网页弹幕插件](http://www.jq22.com/jquery-info7470)

```javascript
<script src=" http://cdn.jtahstu.com/jquery.barrager.min.js"></script>
var item = {
	img: "{{$config['indexBarragerImg']}}", //图片
	info: "{{$config['indexBarragerInfo']}}", //文字
	href: "{{$config['indexBarragerLink']}}", //链接
	speed: 6, //延迟,单位秒,默认6
	// bottom:70, //距离底部高度,单位px,默认随机
	color: '#fff', //颜色,默认白色
	old_ie_color: '#000000', //ie低版兼容色,不能与网页背景相同,默认黑色
}
$('body').barrager(item);
```

本人这里也就是用到了一些基本的功能，这里的文字，图片地址和链接采用的是配置项，方便以后的修改。

#### 2.4.3、通知提示框
该通知提示框采用的是overhang.js插件，Github地址：[https://github.com/paulkr/overhang.js](https://github.com/paulkr/overhang.js)

你也可以参考这篇文章和演示 [jQuery通知提示插件overhang.js](http://www.jq22.com/jquery-info9193)

```javascript
<script type="text/javascript" src="http://tool.usta.wiki/public/js/overhang.min.js"></script>
```
```
$('body').overhang({
	type: 'success',
	message: "{{$config['indexMessage']}}",
	duration: 5,
	upper: true,
});
```

#### 2.4.4、首页控制器代码
```php
	/*
	 * 路由过来的/
	 * 功能：处理首页显示
	 * 返回：首页视图
	 */
    function index(){
    	$sharePaginate=$this->getSharePaginate();
    	return view('Compile/index',['config'=>$this->getConfig(),'sharePaginate'=>$sharePaginate]);
    }
```
这里所带的两个参数，在每个视图里都得有，一个是整个网站的配置项，一个是导航栏的分页对象

### 2.5、代码编辑页
这个页面包括，上半部分的代码编辑器和下半部分的结果显示域

#### 2.5.1、代码编辑页控制器代码
```php
    /*
	 * 路由过来的/compile/{id}，由该函数处理
	 * 功能：处理显示编辑器页面所需的数据
	 * 		1、动态生成生成代码模板
	 * 		2、这里对第二种，即html/css/js特殊处理，因为该视图处理方式不同于其他页面
	 * 返回：代码编辑视图
	 */
    function solve($id){
    	$sharePaginate=$this->getSharePaginate();
    	$lang=$this->getLanguage($id);
    	$template=$this->getTemplate($id);
 		$realTemplate=str_replace('{{date}}',date('Y年m月d日 H:i:s'),$template[0]->template);
 		$realTemplate=str_replace('{{year}}',date('Y'),$realTemplate);  
    	if($id=='2'){
    		return view('Compile/html',['config'=>$this->getConfig(),'sharePaginate'=>$sharePaginate,'value'=>$lang[0]->value,'lang'=>$lang[0]->language,'mode'=>$lang[0]->mode,'template'=>$realTemplate]);
    	}else{
    		return view('Compile/editor',['config'=>$this->getConfig(),'sharePaginate'=>$sharePaginate,'value'=>$lang[0]->value,'lang'=>$lang[0]->language,'mode'=>$lang[0]->mode,'template'=>$realTemplate]);
    	}
    }
	//根据id获取语言
    function getLanguage($id){
    	$language = DB::select('select * from tool_lang where id = ?', [$id]);
    	return $language;
    }
    //获取各语言的默认模板
    function getTemplate($id){
    	$template = DB::select('select * from tool_temp where id = ?',[$id]);
    	return $template;
    }
```

#### 2.5.2、16种编程语言的模板页
这里16种编程语言（除html/css/js）的页面都是采用的这一模板，依据不同的链接，加载不同的语言模块和代码模板
```html
<div class="container">
    <div id="compile-lang" align="center">
		{{$config['editorTitle']}}
		<span id=""> @yield('lang') </span>
		<button type="button" class="btn btn-success col-md-offset-2" id="compile-run">
	    	<i class="glyphicon glyphicon-play"></i>&nbsp;运行
		</button>
		<button type="button" class="btn btn-info" id="compile-share">
			<i class="glyphicon glyphicon-share"></i>&nbsp;分享
		</button>
		<button type="button" class="btn btn-success" id="compile-share-again">
		    <i class="glyphicon glyphicon-share"></i>&nbsp;确认分享
		</button>
	</div>
	<div class="col-md-8 col-md-offset-2" id="compile-share-title">
		<input type="text" class="form-control" id="compile-share-title-input" placeholder="请输入标题...">
	</div>
	<div class="" id="compile-editor-div">
		<div id="compile-editor" name="" class=" form-control">@yield('content')</div>
	</div>
	<div id="tishi"></div>
	<div class="">
		<textarea name="" id="compile-output" class="form-control"></textarea>
	</div>
</div>
```

```javascript
<?php
	if (isset($_GET['h'])) $editorHeight = $_GET['h'];
	else $editorHeight = $config['editorHeight'];
?>
$('#compile-editor').height(<?php echo $editorHeight; ?>);
	require("ace/ext/old_ie");
	ace.require("ace/ext/language_tools");
	var editor = ace.edit("compile-editor");
	editor.$blockScrolling = Infinity;
	editor.setFontSize(14);
	editor.session.setMode("ace/mode/@yield('mode')");
	editor.setOptions({
		enableBasicAutocompletion: true,
		enableSnippets: true,
		enableLiveAutocompletion: true
	});
	//editor.setShowInvisibles(true);
	editor.setTheme("ace/theme/{{$config['editorTheme']}}");
```

有关于ace.js的用法，请参考本人写的另一篇wiki [ACE Editor接入指南](http://git.jtahstu.com/jtahstu/www_usta_wiki/wiki/ACE-Editor%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)

js代码中采用配置项，设置编辑器高度，主题等

#### 2.5.2、HTML/CSS/JS的模板页
该页面需要独立出来，是因为代码运行机制不同

其他部分都是一样的，所以如果你要改页面，**两个都要一起改**，别忘了

```
function show() {
	code = editor.getValue();
	testwin = open("", "testwin", "status=no,menubar=yes,toolbar=no");
	testwin.document.open();
	testwin.document.write(code);
	testwin.document.close();
}
```
点击运行按钮，触发事件，获取到编辑器的代码，新打开个页面，把代码加进去即可显示运行

#### 2.5.3、运行
点击右上角的运行按钮时，会触发.click（）事件，发送ajax请求给控制器

```javascript
$("#compile-run").click(function() {
	layer.ready(function() {
		layer.msg("玩命运行中  <(＾－＾)>");
	});
	$("#compile-output").val('程序正在提交...');
	var code = editor.getValue();
	var value = @yield('value');
	$.ajax({
		type: "post",
		url: "{{URL::to('compiles')}}",
		data: {
			'code': code,
			'language': value
		},
		dataType: 'json',
		success: function(msg) {
    		layer.ready(function() {
				layer.msg("结果出来啦  (*＾-＾*)");
			});
	    	$("#compile-output").val(msg);
		},
		error:function(){
			layer.ready(function() {
				layer.msg("运行发生错误 ╭∩╮(︶︿︶)╭∩╮");
			});
		}
	});
});
```
把代码发送给控制器，控制器获得代码返回执行结果，然后显示在结果域中

中间会有个layer.js的弹出层提示,一般像下面这样使用
```javascript
<script src="http://tool.usta.wiki/public/js/layer.js"></script>
layer.ready(function() {
	layer.msg("玩命运行中  <(＾－＾)>");
});
```
该插件官网地址：[layer官方演示与讲解（jQuery弹出层插件）](http://layer.layui.com/)

#### 2.5.4、分享代码
代码分享前需要补全标题，这里使用的jquery show()和hide()方法,分享成功后异步显示分享链接

```javascript
$("#compile-share-title").hide();
$("#compile-share-again").hide();
$("#compile-share").click(function(){
	$("#compile-share").hide('2000');
    $("#compile-share-again").show('4000');
    $("#compile-share-title").show('4000');
});
$("#compile-share-again").click(function() {
	var title=$("#compile-share-title-input").val();
//		alert(title);
	var code = editor.getValue();
	var value = @yield('value');
	$.ajax({
		type: "post",
		url: "{{URL::to('share')}}",
		data: {
			'title':title,
			'code': code,
			'value': value
		},
		dataType: 'json',
		success: function(msg) {
			layer.ready(function() {
				layer.msg("分享成功，赶紧复制链接分享给你的小伙伴吧  (*＾-＾*)");
			});
			$("#tishi").html("<div class='alert alert-success' style='text-align: center;margin-top: 10px;'>分享成功，链接为 <b>{{URL::to('share')}}/" + msg + "</b></div>");
		},
		error:function(){
			layer.ready(function() {
				layer.msg("啊哦，分享失败了，再试一次吧  (*＾-＾*)");
			});
		}
	});
});
```
#### 2.5.5、分享代码时的控制器代码
```php
	/*
	 * 路由post过来的/share，由该函数处理
	 * 功能：当ajax请求分享代码时，我们需要存储代码到数据库中，并补全其他相关信息
	 * 		 并返回分享的linkid，由前端js补全完整的链接
	 * 返回：ajax代码分享的链接linkid
	 */
    function share(Request $request){	
    	$data=Input::all();
		$config=$this->getConfig();
		$defaultTitle=$config['defaultTitle'];
		$title=trim($data['title'])==""?$defaultTitle:trim($data['title']);
    	$code=$data['code'];
    	$value=$data['value'];
    	$linkid=$this->getShareCount()+1;
    	$time=date('Y-m-d H:i:s');
    	$res=$this->saveShareCode($linkid,$title,$code,$value,$time);
    	if($res){
    		echo json_encode($linkid);
    	}else{
    		echo json_encode('errors');
    	}
    }
	//获取数据库中已经存储有多少分享代码了
	function getShareCount(){
    	$count=DB::select('select count(id) as count from tool_share');
    	return $count[0]->count;
    }
	//保存分享的代码
     function saveShareCode($linkid,$title,$code,$value,$time){
    	$res=DB::insert("insert into tool_share(id,linkid,title,code,value,time,view) values('',?,?,?,?,?,0)",[$linkid,$title,$code,$value,$time]);
    	return $res;
    }
```

### 2.6、代码分享页、代码归档页、讨论交流页
如果你看懂了前面介绍的首页和代码编辑页，这些页面对你也就不成问题了

**一个页面包含头部、导航栏和脚部这三个固定的部分，不同的只是中间部分的代码**

 - 代码分享页中间是一个编辑器，依据链接id从数据库读出代码并填充进去，再次分享和代码编辑页里的分享功能完全一样
 - 代码归档页是把所有分享的代码用一个表格显示出来，其中用到了Laravel提供的分页功能
 - 讨论交流页是引入的畅言，在畅言网站注册个账号，把代码加进来即可

### 2.7、后台登录页
后台登录页有头部，但是没有之前页面的导航栏和脚部，所以只需要传入一个配置项即可，不需要分页对象

**这里也没有真正的用户登录，只是在链接中加入明文的root和MD5加密的密码值，由后台页面去判断该密码是否正确**
```javascript
<dl class="admin_login">
	<dt>
	  <strong>iTool管理系统</strong>
	  <em>iTool Management System</em>
	 </dt>
	<dd class="user_icon">
		<input placeholder="账号" class="login_txtbx" type="text" id="name">
	</dd>
	<dd class="pwd_icon">
		<input placeholder="密码" class="login_txtbx" type="password" id="pass">
	</dd>
	<dd>
		<input value="立即登陆" class="submit_btn" type="button" id="login">
	</dd>
</dl>
<script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="http://tool.usta.wiki/public/js/jQuery.md5.js"></script>
<script>
	$(document).ready(function() {
		$('#login').click(function(){
		var name=$('#name').val();
		var pass=$('#pass').val();
			window.location.href='{{URL::to('/admin')}}?user=root&pass='+$.md5(pass);
		});
	});
</script>
```
后台验证代码
```php
<?php
	if(isset($_GET['user'])&&isset($_GET['pass'])){
		$passMD5=$_GET['pass'];
		if($passMD5!=md5('你要设置的密码')){
			header("Location:URL::to('/login')");
			exit;
		}
	}else{
		header("Location:URL::to('/login')");
		exit;
	}
?>
```
因为这个只是给自己用的，就不写登录的模块了，省事。

### 2.8、管理后台
后台采用的是Bootstrap Affix插件来写的页面

**显示所有的配置选项，然后把当前的配置加载进去，当要修改配置时，使用ajax的方式提交修改**

由于Bootstrap页面缓存的问题，修改完成之后页面可能是不变的，所以**需要重新打开这个页面，不是刷新哦**

举一个例子来说，其他的代码省略了，如全局配置的设置
html代码
```html
<div class="col-sm-10" id="admin-section">
	<h2 id="section-1">全局配置</h2>
	<div id="admin-global">
		<div class="form-horizontal" role="form">
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">网站标题：</label>
				<div class="col-sm-10">
					<input type="text" class="form-control" id="admin-global-title" value="{{$config['title']}}">
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">网站关键词：</label>
				<div class="col-sm-10">
					<input type="text" class="form-control" id="admin-global-key" value="{{$config['keyword']}}">
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">网站介绍：</label>
				<div class="col-sm-10">
					<input type="text" class="form-control" id="admin-global-des" value="{{$config['des']}}">
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">网站图标：</label>
				<div class="col-sm-10">
					<input type="text" class="form-control" id="admin-global-icon" value="{{$config['icon']}}">
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">当前版本：</label>
				<div class="col-sm-10">
					<input type="text" class="form-control" id="admin-global-version" value="{{$config['version']}}">
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">统计代码：</label>
				<div class="col-sm-10">
					<textarea class="form-control" rows="3" id="admin-global-cnzz">{{$config['cnzz']}}</textarea>
				</div>
			</div>
			<div class="form-group">
				<label for="" class="col-sm-2 control-label">添加头部代码：</label>
				<div class="col-sm-10">
					<textarea class="form-control" rows="3" id="admin-global-headAddCode">{{$config['headAddCode']}}</textarea>
				</div>
			</div>
			<div class="form-group">
				<div class="col-sm-offset-2 col-sm-10">
					<button type="submit" class="btn btn-success" id="admin-global-submit">
					<i class="glyphicon glyphicon-ok"></i> 确认修改
					</button>
				</div>
			</div>
		</div>
	</div>
</div>
```
ajax代码
```javascript
$('#admin-global-submit').click(function() {
	var title = $('#admin-global-title').val();
	var key = $('#admin-global-key').val();
	var des = $('#admin-global-des').val();
	var icon = $('#admin-global-icon').val();
	var version = $('#admin-global-version').val();
	var cnzz = $('#admin-global-cnzz').val();
	var headAddCode = $('#admin-global-headAddCode').val();
	//		alert(title + '\n' + key);
	$.ajax({
		type: "post",
		url: "{{URL::to('/admin/global')}}",
		data: {
			'title': title,
			'key': key,
			'des': des,
			'icon': icon,
			'version': version,
			'cnzz': cnzz,
			'headAddCode': headAddCode
		},
		success: function(msg) {
			var prompt=(msg=='1')?'修改成功  (*＾-＾*)':'修改失败 ○|￣|_';
			layer.ready(function() {
				layer.msg(prompt);
			});
		}
	});
});
```
php代码
```php
 /*
  * 路由post过来的/admin/{func}
  * 返回：ajax网站配置数据是否修改成功
  */
 
 function adminFunction($func){
 	$data=Input::all();
	if($func=='global'){
		$title=htmlentities($data['title']);
		$key=htmlentities($data['key']);
		$des=htmlentities($data['des']);
		$icon=htmlentities($data['icon']);
		$version=htmlentities($data['version']);
		$cnzz=htmlentities($data['cnzz']);
		$headAddCode=htmlentities($data['headAddCode']);
		$res=$this->setConfigGlobal($title,$key,$des,$icon,$version,$cnzz,$headAddCode);
		echo $res?'1':'0';
	}
 }
function setConfigGlobal($title,$key,$des,$icon,$version,$cnzz,$headAddCode){
	$res1=DB::table('tool_config')->where('k', 'title')->update(['value' => $title]);
	$res2=DB::table('tool_config')->where('k', 'keyword')->update(['value' => $key]);
	$res3=DB::table('tool_config')->where('k', 'des')->update(['value' => $des]);
	$res4=DB::table('tool_config')->where('k', 'icon')->update(['value' => $icon]);
	$res5=DB::table('tool_config')->where('k', 'version')->update(['value' => $version]);
	$res6=DB::table('tool_config')->where('k', 'cnzz')->update(['value' => $cnzz]);
	$res7=DB::table('tool_config')->where('k', 'headAddCode')->update(['value' => $headAddCode]);
	return $res1||$res2||$res3||$res4||$res5||$res6||$res7;
}
```
**当接收post过来的配置项，在把代码存入数据库之前，会进行htmlentities特殊字符编码，以防止注入攻击
所以在视图加载配置时，但凡有html或js或者其他的有特殊字符的代码时，我们都需要解码
而且不要用{{...}}，这个是默认使用htmlentities特殊字符编码的，我们使用{!!...!!}的方式
例如 `{!! htmlspecialchars_decode($config['title']) !!}`**

## 三、路由

```php
<?php
//show home page
Route::get('/','CompileController@index');

//show editor view
Route::get('/compile/{id}','CompileController@solve');

//run and share
Route::post('/compiles','CompileController@result');
Route::post('/share','CompileController@share');

//show share code view
Route::get('/share/{linkid}','CompileController@showShare');

//show code archive
Route::get('/share','CompileController@shareList');

//show discuss view
Route::get('/discuss','ToolController@discuss');

//show login view
Route::get('/login','ToolController@login');

//show admin view
Route::get('/admin','ToolController@admin');

//admin ajax
Route::post('/admin/{func}','ToolController@adminFunction');
```

## 四、数据库
MySQL数据库表结构
![数据库表结构][5]
```
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for tool_code
-- ----------------------------
DROP TABLE IF EXISTS `tool_code`;
CREATE TABLE `tool_code` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `code` text,
  `type` int(11) DEFAULT NULL,
  `time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=93 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Table structure for tool_config
-- ----------------------------
DROP TABLE IF EXISTS `tool_config`;
CREATE TABLE `tool_config` (
  `k` varchar(255) DEFAULT NULL,
  `value` varchar(1023) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

-- ----------------------------
-- Table structure for tool_lang
-- ----------------------------
DROP TABLE IF EXISTS `tool_lang`;
CREATE TABLE `tool_lang` (
  `id` int(11) NOT NULL,
  `language` varchar(255) DEFAULT NULL,
  `value` int(11) DEFAULT NULL,
  `mode` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Table structure for tool_share
-- ----------------------------
DROP TABLE IF EXISTS `tool_share`;
CREATE TABLE `tool_share` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `linkid` int(11) DEFAULT NULL,
  `title` varchar(255) DEFAULT NULL,
  `code` text,
  `value` int(11) DEFAULT NULL,
  `time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
  `view` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Table structure for tool_temp
-- ----------------------------
DROP TABLE IF EXISTS `tool_temp`;
CREATE TABLE `tool_temp` (
  `id` int(11) NOT NULL,
  `template` text,
  PRIMARY KEY (`id`),
  CONSTRAINT `lang` FOREIGN KEY (`id`) REFERENCES `tool_lang` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
SET FOREIGN_KEY_CHECKS=1;
```
具体sql文件在项目MySQL目录里，导入最新的版本即可

ok，文档到此就告一段落了，这个小项目也到此结束了，by jtahstu at 2016/10/09 in USTA Lixing Building Lab .



  [1]: http://cdn.jtahstu.com/iSchool/iTool/iToolFunc1.jpg
  [2]: http://cdn.jtahstu.com/iSchool/iTool/iToolFunc2.png
  [3]: http://cdn.jtahstu.com/iSchool/iTool/iToolPages.png
  [4]: http://cdn.jtahstu.com/iSchool/iTool/iToolIcons.png
  [5]: http://cdn.jtahstu.com/iSchool/iTool/iToolMySQL.png