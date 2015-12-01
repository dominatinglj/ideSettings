#  ideSettings

For now, there's only **webstorm** settings. I exported this settings to make sure the ide settings in my workplace and my pc are the same.
For I am an front-end engineer, I am working with *html* & *css* & *javascript* and sth. among them.
By using these settings:
- You should choose the **Scheme** of my name -- *Geoffrey* in preferences in "**Code Style**" and "**Colors & Fonts**".
- There are some templates in "**File and Code Templates**". I picked them up [below](#templates).
- There are two types of keymap in "**Keymap**" named *MyMac* and *MyWindows* which are for Mac or Windows.

----
##Templates
"**File and Code Templates**" settings for some examples of frequently-used code or reset code.
- js:
    ```javascript
    /* 全局的变量方法*/
    var globalAction = {
        init: function () {
            this.snifBrowser.init();
            this.fixPlaceholder();
            this.lazyLoad.init('j-img');
        },
        snifBrowser: {  /* 判断浏览器的类型*/
            hasRun: false,
            isWebkit: false,
            isSafari: false,
            isIDevice: false,
            isIpad: false,
            isIphone: false,
            isAndroid: false,
            isMobile: false,
            isWechat: false,
            device: '',
            version: '',
            standalone: '',
            init: function () {
                this.hasRun = true;
    
                var navigator = window.navigator,
                    userAgent = navigator.userAgent,
                    ios = userAgent.match(/(iPad|iPhone|iPod)[^;]*;.+OS\s([\d_\.]+)/),
                    android = userAgent.match(/(Android)[\s|\/]([\d\.]+)/);
    
                this.isWebkit = /WebKit\/[\d.]+/i.test(userAgent);
                this.isSafari = ios ? (navigator.standalone ? this.isWebkit : (/Safari/i.test(userAgent) && !/CriOS/i.test(userAgent) && !/MQQBrowser/i.test(userAgent))) : false;
    
                if (ios) {
                    this.device = ios[1];
                    this.version = ios[2].replace(/_/g, '.');
                    this.isIDevice = (/iphone|ipad|ipod/i).test(navigator.appVersion);
                    this.isIpad = userAgent.match(/iPad/i);
                    this.isIphone = userAgent.match(/iPhone/i);
                } else if (android) {
                    this.version = android[2];
                    this.isAndroid = (/android/i).test(navigator.appVersion);
                }
    
                this.isMobile = this.isAndroid || this.isIDevice;
                this.standalone = navigator.standalone;
                this.isWechat = userAgent.indexOf("MicroMessenger") >= 0;
            }
        },
        fixPlaceholder: function () {   /* 修复placeholder属性*/
            var $input = $('input, textarea');
    
            if (!('placeholder' in document.createElement("input"))) {
                $input.each(function (index, element) {
                    var placeText = $(element).attr('placeholder');
    
                    if ($(element).val() === '') {
                        $(element).val(placeText);
                    }
    
                    $(this).on('focus', function () {
                        if ($(this).val() === placeText) {
                            $(this).val('');
                        }
                    }).on('blur', function () {
                        if ($(this).val() === '') {
                            $(this).val(placeText);
                        }
                    });
                });
            }
        },
        lazyLoad: {
            timeoutId: null,
            init: function (className) {
                var self = this;
    
                self.bindLazyLoad(className);
    
                $(window).on('scroll', function () {
                    self.bindLazyLoad(className);
                });
            },
            bindLazyLoad: function (className) {    /* 触发*/
                var self = this;
    
                clearTimeout(self.timeoutId);
                self.timeoutId = setTimeout(function () {
                    self.imgLazyLoad(self.screamScope(className),className);
                }, 500);
            },
            screamScope: function (className, offset) { /* 获取屏幕内的dom数组*/
                var $all = $('.' + className),
                    minHeight = document.body.scrollTop || document.documentElement.scrollTop,
                    maxHeight = minHeight + $(window).height(),
                    domArr = [];
    
                if (typeof offset !== 'number') {
                    offset = 50;
                }
    
                $all.each(function (index, element) {
                    var elemHeight = $(element).offset().top;
    
                    if (elemHeight <= maxHeight + offset && elemHeight >= minHeight - offset) {
                        domArr.push(element);
                    }
                });
    
                return domArr;
            },
            imgLazyLoad: function (domArr, className) { /* 图片延时加载*/
                $.each(domArr, function (index, value) {
                    var src = $(value).attr('data-src'),
                        newImg = new Image();
    
                    if (src !== undefined) {
                        newImg.src = src;
    
                        /* 重新加载或缓存加载*/
                        if (newImg.complete) {  /* 缓存加载*/
                            $(value).attr('src', src)
                                .removeAttr('data-src')
                                .removeClass(className);
                        } else {
                            newImg.onload = function () {   /* 新加载*/
                                $(value).attr('src', src)
                                    .removeAttr('data-src')
                                    .removeClass(className);
                            };
                        }
                    }
                });
            }
        },
        addFavorite: function (url, title) {  /* 加入收藏夹,url必须带有协议头*/
            if (window.external && 'addFavorite' in window.external) {
                window.external.addFavorite(url, title);
            } else if (window.sidebar && window.sidebar.addPanel) {
                window.sidebar.addPanel(url, title);
            } else if (window.opera && window.print) {
                this.title = title;
                return true;
            } else {
                alert('加入收藏失败，请使用' + (navigator.userAgent.toLowerCase().indexOf('mac') !== -1 ? 'Command/Cmd' : 'Ctrl') + '+D 进行添加');
            }
        },
        isIE: function (num) {  /* 判断IE版本*/
            var dom = document.createElement("b");
    
            dom.innerHTML = "<!--[if IE " + num + "]><i></i><![endif]-->";
    
            return dom.getElementsByTagName("i").length;
        },
        animateToTop: function (scrollTo, time) {  /* 模拟animate滚动到顶部*/
            var scrollFrom = parseInt(document.body.scrollTop),
                i = 0,
                runEvery = 5;
    
            scrollTo = parseInt(scrollTo);
            time /= runEvery;
    
            var interval = setInterval(function () {
                i++;
                document.body.scrollTop = (scrollTo - scrollFrom) / time * i + scrollFrom;
                if (i >= time) {
                    clearInterval(interval);
                }
            }, runEvery);
        },
        cookie: {
            getCookie: function (name) {   /* 获取指定cookie*/
                var cookieArr = document.cookie.split("; "),
                    cookieValue,
                    i,
                    temArr;
    
                for (i = 0; i < cookieArr.length; i++) {
                    temArr = cookieArr[i].split("=");
                    if (name === temArr[0]) {
                        cookieValue = unescape(temArr[1]);
                        break;
                    }
                }
    
                return cookieValue;
            },
            setCookie: function (c_name, value, days) {   /* 设置cookie*/
                var expiresDays = new Date();
    
                expiresDays.setDate(expiresDays.getDate() + days);
                document.cookie = c_name + "=" + escape(value) + ((typeof days === 'number') ? ';expires=' + expiresDays.toGMTString() : '');
            }
        },
        lowerVersion: function (version, base) { /* 判断version是否较低*/
            var i,
                arr1 = version.toString().split('.'),
                arr2 = base.toString().split('.'),
                length = arr1.length > arr2.length ? arr1.length : arr2.length;
    
            for (i = 0; i < length; i++) {
                if (arr1[i] !== arr2[i]) {
    
                    return parseInt(arr1[i]) < parseInt(arr2[i]);
                }
            }
    
            return false;   /* 两值相同*/
        }
    };
    
    $(function () {
        /* 初始化*/
        globalAction.init();
    
    
    });
    ```
- css
    ```css
    /* CSS reset */
    html {-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;}
    html,body,div,span,object,iframe,h1,h2,h3,h4,h5,h6,p,a,img,button,
    dl,dt,dd,ul,ol,li,table,caption,tbody,tfoot,thead,tr,th,td,
    video,audio,footer,header,pre,code,form,fieldset,legend,input,textarea,blockquote,q,
    article,aside,details,figcaption,figure,hgroup,menu,nav,section {margin:0;padding:0;border:0;font-size:100%;vertical-align:baseline;}
    body,button,input,select,textarea {font:14px/1.4286 "microsoft yahei",simsun,tahoma,arial,sans-serif;}
    h1,h2,h3,h4,h5,h6 {font-size:100%;font-weight:500;}
    a,ins {text-decoration:none;}
    button,input,select,textarea,:focus {outline:none;}
    audio,canvas,video {display:inline-block;*display:inline;zoom:1;}
    table {border-collapse:collapse;border-spacing:0;}
    ol,ul {list-style:none;}
    hr {display:block;height:1px;border:0;border-top:1px solid #ccc;margin:1em 0;padding:0;}
    abbr,acronym {border:0;font-variant:normal;}
    del {text-decoration:line-through;}
    address,caption,cite,code,dfn,th,var,em,i,b,time {font-style:normal;font-weight:500;}
    caption,th {text-align:left;}
    blockquote,q {quotes:none;}
    blockquote:before,blockquote:after,q:before,q:after {content:'';content:none;}
    sub,sup {font-size:75%;line-height:0;position:relative;vertical-align:baseline;}
    sup {top:-0.5em;}
    sub {bottom:-0.25em;}
    article,aside,details,figcaption,figure,footer,header,hgroup,menu,nav,section,summary,iframe {display:block;}
    
    img {max-width: 100%;}
    a,button,input,textarea {-webkit-tap-highlight-color:rgba(0,0,0,0);}
    /*/CSS reset */
    /* 全局*/
    html, body {
      /*height: 100%;*/
    }
    body {
      background-color: #fff;
      color: #333;
      min-width: ;
      position: relative;
    }
    a {
      color: #333;
    }
    a:hover {
    
    }
    .left {
      float: left;
      _display: inline;
    }
    .right {
      float: right;
      _display: inline;
    }
    .clearfix:after {
      display: block;
      clear: both;
      content: ".";
      visibility: hidden;
      height: 0;
    }
    .clearfix {
      zoom: 1;
    }
    .ellipsis {
      _width: 100%;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
    .complex_ellipsis {
      line-height: 1;
      height: 2em;
      display: block;
      display: -webkit-box;
      *display: block;
      overflow: hidden;
      text-overflow: ellipsis;
      -webkit-box-orient: vertical;
      -webkit-line-clamp: 2;
    }
    /*/全局*/
    /* 大图格式架构*/
    .full_bg {
      background: url() #fff center 0 no-repeat;
      min-width: ;
      overflow: hidden;
    }
    .header,
    .main {
      width: ;
      margin: 0 auto;
      position: relative;
    }
    .footer {
      background-color: #fff;
      min-width: ;
    }
    .footer .wrap {
      width: ;
      margin: 0 auto;
    }
    /*/大图格式架构*/
    /* 头像-png24兼容ie6*/
    .avatar {
      width: ;
      height: ;
      position: relative;
      cursor: pointer;
    }
    .avatar img {
      background: url() 0 0 no-repeat; /* 默认图*/
      width: ;
      height: ;
      display: block;
    }
    .avatar span.hover,
    .avatar span.normal {
      width: ;
      height: ;
      position: absolute;
      top: 0;
      left: 0;
    }
    .avatar span.hover {
      background: url() 0 0 no-repeat;
    }
    .avatar span.normal {
      background: url() 0 0 no-repeat;
    }
    /*/头像-png24兼容ie6*/
    ```
  
- html
    ```html
    <!DOCTYPE html>
    <html lang="zh-cmn-Hans">
    <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
      <meta name="renderer" content="webkit">
      <meta http-equiv="Cache-Control" content="no-siteapp">
      <!--<link rel="dns-prefetch" href="">-->
      <!--<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">-->
      <!--<meta name="format-detection" content="telephone=no,email=no,address=no">-->
      <meta name="keywords" content="">
      <meta name="description" content="">
      <link rel="shortcut icon" type="image/ico" href="">
      <title>#[[$Title$]]#</title>
      <link href="" type="text/css" rel="stylesheet">
      <script>
          /* 兼容不支持console.log到alert*/
          if (typeof console === "undefined" || typeof console.log === "undefined") {
              console = {};
              console.log = function (msg) {
                  alert(msg);
              };
          }
      </script>
    </head>
    <body>
    <!-- 大图格式-->
    <div class="full_bg">
      <div class="header">
    
      </div>
      <div class="main">
    
      </div>
    </div>
    <div class="footer">
      <div class="wrap">
         
      </div>
    </div>
    <!--/大图格式-->
    
    <!--[if IE 6]>
      <script type="text/javascript" src="js/pngfilter.js"></script>
      <script type="text/javascript">DD_belatedPNG.fix('.png');</script>
    <![endif]-->
    <script type="text/javascript" src=""></script>
    <script type="text/javascript">
      $(function () {
          
      })
    </script>
    </body>
    </html>
    ```
- gulp settings for tasks
    ```javascript
    //引入gulp及组件
    var gulp = require('gulp'),
      rename = require('gulp-rename'),           //重命名
      imagemin = require('gulp-imagemin'),       //图片压缩
      pngquant = require('imagemin-pngquant'),   //深度压缩png图片
      minifyCss = require('gulp-minify-css'),    //css压缩
      uglify = require('gulp-uglify'),           //js压缩
      makeUrlVer = require('gulp-make-css-url-version'),  //图片添加版本号
      replace = require('gulp-replace'),          //文本替换
      concat = require('gulp-concat');           //合并文件
    
    
    //css任务
    gulp.task('doCss', function () {
      gulp.src(['../dev/css/pc.css', '../dev/css/wap.css'])
          //.pipe( concat('pc.min.css') )    //合并文件
          .pipe(makeUrlVer())        // 自动添加版本号
          .pipe(minifyCss({         //压缩
              advanced: false,       //类型：Boolean 默认：true [是否开启高级优化（合并选择器等）]
              compatibility: 'ie7',  //类型：String 默认：''or'*' [启用兼容模式； 'ie7'：IE7兼容模式，'ie8'：IE8兼容模式，'*'：IE9+兼容模式]
              keepBreaks: false      //类型：Boolean 默认：false [是否保留换行]
          }))
          .pipe(gulp.dest('../release/css/'));  //输出地址
    });
    
    //js压缩任务
    gulp.task('doJs', function () {
      gulp.src(['../dev/js/pc.js', '../dev/js/wap.js'])
          .pipe(uglify({ //压缩
              mangle: true,//类型：Boolean 默认：true 是否修改变量名
              compress: true//类型：Boolean 默认：true 是否完全压缩
          }))
          .pipe(gulp.dest('../release/js/'));
    });
    
    //图片压缩任务
    gulp.task('doImage', function () {
      gulp.src('../images/dev/*')
          .pipe(imagemin({
              optimizationLevel: 5, //类型：Number  默认：3  取值范围：0-7（优化等级）
              progressive: true, //类型：Boolean 默认：false 无损压缩jpg图片
              interlaced: true, //类型：Boolean 默认：false 隔行扫描gif进行渲染
              multipass: true, //类型：Boolean 默认：false 多次优化svg直到完全优化
              use: [pngquant()] //使用pngquant深度压缩png图片
          }))
          .pipe(gulp.dest('../images/pc/'));
    });
    
    
    //default
    gulp.task('default', ['doCss', 'doJs', 'doImage']);
    
    //监视文件
    gulp.task('watch', function () {
      gulp.watch('../dev/css/!*.css', ['doCss']);
      gulp.watch('../dev/js/!*.js', ['doJs']);
      gulp.watch('../dev/images/!**', ['doImage']);
    });
    ```
  
- node's package for gulp
    ```json
    {
      "name": "${PROJECT_NAME}",
      "version": "1.0.0",
      "author": "${USER}",
      "description": "my gulp tools",
      "devDependencies": {
          "gulp": "^3.9.0",
          "gulp-concat": "^2.6.0",
          "gulp-imagemin": "^2.3.0",
          "gulp-load-plugins": "^1.0.0",
          "gulp-make-css-url-version": "0.0.12",
          "gulp-minify-css": "^1.2.1",
          "gulp-rename": "^1.2.2",
          "gulp-replace": "^0.5.4",
          "gulp-uglify": "^1.4.2"
      },
      "dependencies": {
          "imagemin-pngquant": "^4.2.0"
      }
    }
    ```
