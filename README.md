### 响应式网站-微金所

***

#### 项目预览

![all](https://github.com/yangmiemie1/weijinsuo/blob/master/images/all.jpg)

#### 技术点

+ bootstrap
+ 字体图标的使用

+ 栅格系统    行和列的布局，网格状布局  

+ 媒体查询    针对不同屏幕区间设置不同的布局和样式  

+ visible-lg   响应式工具  显示隐藏  （3.2版本之后建议使用hidden-lg）

+ 轮播图插件   

+ ajax 获取数据   获取数据做动态响应式轮播图。减少浏览器的请求次数
+ art-template 模板引擎   
+ 数据缓存  window.data = data    
+ 移动端手势切换  touchstart  touchmove  touchend

#### 实现功能

#### 顶部通栏 

html代码 

主要使用bootstrap栅栏布局，并且使用：hidden-sm hidden-xs设置在小屏设备和超小屏设备中隐藏

```
<header class="wjs_topBar hidden-sm hidden-xs">
    <div class="container">
        <div class="row">
            <div class="col-md-2">
                <a href="#" class="wjs_app">
                    <span class="wjs_icon wjs_icon_phone"></span>
                    <span>手机微金所</span>
                    <span class="glyphicon glyphicon-menu-down"></span>
                    <img src="images/code.jpg" alt="">
                </a>
            </div>
            <div class="col-md-5">
                <span class="wjs_icon wjs_icon_tel"></span>
                <span> 4006-89-4006（服务时间：9:00-21:00）</span>
            </div>
            <div class="col-md-2">
                <a href="#">常见问题</a>
                <a href="#">财富登录</a>
            </div>
            <div class="col-md-3">
                <a href="#" class="btn btn-default btn-register">免费注册</a>
                <a href="#" class="btn btn-link btn-login">登录</a>
            </div>
        </div>
    </div>
</header>
```

#### 导航栏部分

html代码     使用navbar插件

````
<!--导航栏-->
<nav class="navbar wjs_nav" data-spy="affix" data-offset-top="200">
    <div class="container">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">
                <span class="wjs_icon wjs_icon_logo"></span>
                <span class="wjs_icon wjs_icon_name"></span>
            </a>
        </div>
        <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
            <ul class="nav navbar-nav">
                <li class="active"><a href="#">我要投资</a></li>
                <li><a href="#">我要借贷</a></li>
                <li><a href="#">平台介绍</a></li>
                <li><a href="#">新手专区</a></li>
                <li><a href="#">最新动态</a></li>
                <li><a href="#">微平台</a></li>
            </ul>
            <ul class="nav navbar-nav navbar-right hidden-sm">
                <li><a href="#">个人中心</a></li>
            </ul>
        </div>
    </div>
</nav>
````

#### 轮播图部分

+ carousel  轮播图模块

+ slide   是给当前组件加上滑动效果
+ data-ride="carousel"  的功能是初始化轮播图属性

```
<section class="wjs_banner">
    <div id="carousel-example-generic" class="carousel slide" data-ride="carousel" >
        <ol class="carousel-indicators">

        </ol>
        <div class="carousel-inner">

        </div>
        <a class="left carousel-control" href="#carousel-example-generic"  data-slide="prev">
            <span class="glyphicon glyphicon-chevron-left"></span>
        </a>
        <a class="right carousel-control" href="#carousel-example-generic" data-slide="next">
            <span class="glyphicon glyphicon-chevron-right"></span>
        </a>
    </div>
</section>
```

js代码

- 1.获取轮播图数据    ajax
- 2.根据数据动态渲染  根据当前设备  屏幕宽度判断 
- 2.1 准备数据
  - 2.2 把数据转换成html格式的字符串 （动态创建元素，字符串拼接，模版引擎【artTemplate】
  - 2.3 把字符渲染页面当中*/

+ 3.测试功能 页面尺寸发生改变重新渲染

+ 4.移动端手势切换  touch

```
$(function () {
    /*动态轮播图*/
    banner();
    /*移动端页签*/
    initMobileTab();
    /*初始工具提示*/
    $('[data-toggle="tooltip"]').tooltip();
});
var banner = function () {
    /*做数据缓存*/
    var getData = function (callback) {
        /*缓存了数据*/
        if(window.data){
            callback && callback(window.data);
        }else {
            /*1.获取轮播图数据*/
            $.ajax({
                type:'get',
                url:'js/data.json',
                /*设置同步执行*/
                /*async:false,*/
                /*强制转换后台返回的数据为json对象*/
                /*强制转换不成功程序报错，不会执行success,执行error回调*/
                dataType:'json',
                data:'',
                success:function (data) {
                    window.data = data;
                    callback && callback(window.data);
                }
            });
        }
    }
    var render = function () {
        getData(function (data) {
            /*2.根据数据动态渲染  根据当前设备  屏幕宽度判断 */
            var isMobile = $(window).width() < 768 ? true : false;
            //console.log(isMobile);
            /*2.1 准备数据*/
            /*2.2 把数据转换成html格式的字符串*/
            /*使用模版引擎：那些html静态内容需要编程动态的*/
            /*发现：点容器  图片容器  新建模版*/
            /*开始使用*/
            /*<% console.log(list); %> 模版引擎内不可使用外部变量 */
            var pointHtml = template('pointTemplate',{list:data});
            //console.log(pointHtml);
            var imageHtml = template('imageTemplate',{list:data,isMobile:isMobile});
            //console.log(imageHtml);
            /*2.3 把字符渲染页面当中*/
            $('.carousel-indicators').html(pointHtml);
            $('.carousel-inner').html(imageHtml);
        });
    }
    /*3.测试功能 页面尺寸发生改变事件*/
    $(window).on('resize',function () {
        render();
        /*通过js主动触发某个事件*/
    }).trigger('resize');
    /*4.移动端手势切换*/
    var startX = 0;
    var distanceX = 0;
    var isMove = false;
    /*originalEvent 代表js原生事件*/
    $('.wjs_banner').on('touchstart',function (e) {
        startX = e.originalEvent.touches[0].clientX;
    }).on('touchmove',function (e) {
        var moveX = e.originalEvent.touches[0].clientX;
        distanceX = moveX - startX;
        isMove = true;
    }).on('touchend',function (e) {
        /*距离足够 50px 一定要滑动过*/
        if(isMove && Math.abs(distanceX) > 50){
            /*手势*/
            /*左滑手势*/
            if(distanceX < 0){
                $('.carousel').carousel('next');
            }
            /*右滑手势*/
            else {
                $('.carousel').carousel('prev');
            }
        }
        startX = 0;
        distanceX = 0;
        isMove = false;
    });
}
var initMobileTab = function () {
    /*1.解决换行问题*/
    var $navTabs = $('.wjs_product .nav-tabs');
    var width = 0;
    $navTabs.find('li').each(function (i, item) {
        var $currLi = $(this);//$(item);
        /*
        * width()  内容
        * innerWidth() 内容+内边距
        * outerWidth() 内容+内边距+边框
        * outerWidth(true) 内容+内边距+边框+外边距
        * */
        var liWidth = $currLi.outerWidth(true);
        width += liWidth;
    });
    console.log(width);
    $navTabs.width(width);

    /*2.修改结构使之区域滑动的结构*/
    //加一个父容器给 .nav-tabs 叫  .nav-tabs-parent

    /*3.自己实现滑动效果 或者 使用iscroll */
    new IScroll($('.nav-tabs-parent')[0],{
        scrollX:true,
        scrollY:false,
        click:true
    });
}

```

模板引擎的使用

```
<!--使用模版引擎-->
<script type="text/template" id="pointTemplate">
    <% for(var i = 0 ; i < list.length; i ++){ %>
        <li data-target="#carousel-example-generic" data-slide-to="<%=i%>" class="<%=i==0?'active':''%>"></li>
    <% } %>
</script>
<script type="text/template" id="imageTemplate">
    <% for(var i = 0 ; i < list.length; i ++){ %>
    <div class="item <%=i==0?'active':''%>">
        <% if(isMobile){ %>
        <a href="#" class="m_imgBox"><img src="<%=list[i].mUrl%>" alt=""></a>
        <% }else{ %>
        <a href="#" class="pc_imgBox" style="background-image: url(<%=list[i].pcUrl%>)"></a>
        <% } %>
    </div>
    <% } %>
</script>
```





#### 信息模块

#### 产品模块

#### 预约模块

#### 新闻模块

#### 合作伙伴





