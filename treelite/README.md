# Idea

快速构建

## 大致流程

原始模版 －》编译 －》目标模版

## 意义

* 提升移动页面整体开发效率，通过组件封装减少JS的代码量，通过扩充HTML标签、属性加快页面构建速度
* 加入后端渲染，减少前端DOM操作。
* 一体的组件化思想

__PS__: 目前这想法对于多页面应用还适合，RIA还没想好...

## 实例

### 原始模版

对原始HTML进行扩充，添加自定义标签和自定义属性

```html
    <!DOCTYPE html>
    <html>
        <head>
            <meta-mobile-viewport />
            <meta charset="utf-8" />
        </head>
        <body>
            <nav position="fixed" class="nav-custom">
                <a href="#">Home</a>
                <a href="#">Link1</a>
                <a href="#">Link2</a>
            </nav>

            <banner gesture="slide" direction="hor" pointer="true" datasource="banners" scheme="noraml"></banner>

            <div class="search">
                <suggestion src="/target" />
            </div>

            <list datasource="url(/queryList)" page="true" page-style="scroll">
                <!-- target: placeholder -->
                <div class="loading">Loading...</div>

                <!-- target: item -->
                <div clas="item">
                    <h3>${title}</h3>
                    <p>${info}</p>
                    <p><a href="#">Detail</a>&nbsp;<a href="tel://${tel}">Call Me</a></p>
                    <div style="height:0" scroll="true">${detail}</div>
                </div>
            </list>

            <footer position="fixed">
                <a class="btn">Map</a>
                <a class="btn">Tel</a>
                <a class="btn">Chat</a>
            </footer>
        </body>
    </html>
```

### 目标模版

由`原是模版`通过编译输出，符合目标模版平台规范的模版文件，对自定义的标签与属性进行解析，并自动添加文件依赖。

以下是解析成`smarty`目标模版的代码：

```html
    <!DOCTYPE html>
    <html>
        <head>
            <!-- 由`<meta-mobile-viewport />`解析 -->
            <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no" /> 
            <meta charset="utf-8" />
            <!-- 编译引入CSS文件依赖 -->
            <link type="text/css" rel="stylesheet" href="saber-ui.css" />
            <!-- 编译引入JS文件依赖 -->
            <script src="http://s1.bdstatic.com/r/www/cache/ecom/esl/1-6-6/esl.js"></script>
        </head>
        <body>
            <!-- `position="fixed"` 解析成class引用 -->
            <nav class="nav-custom position-fixed">
                <a href="#">Home</a>
                <a href="#">Link1</a>
                <a href="#">Link2</a>
            </nav>

            <!-- `banner`标签解析 -->
            <div class="banner" data-ui="banner" data-ui-gesture="slide" data-ui-direction="hor" data-ui-pointer="true" data-ui-scheme="noraml">
                {foreach $banners as $item}
                    <img src="$item.src" />
                {/foreach} 
            </div>

            <div class="search">
                <!-- `suggestion`解析 -->
                <input type="text" data-ui="suggestion" data-ui-src="/target" />
            </div>

            <!-- 由`list`标签解析 -->
            <div data-ui="list" data-ui-page="true" ui-page-style="scroll">
                <div class="loading">Loading...</div>
                <div style="display:none" data-ui-template="true">
                    <div clas="item">
                        <h3>${title}</h3>
                        <p>${info}</p>
                        <p><a href="#">Detail</a>&nbsp;<a href="tel://${tel}">Call Me</a></p>
                        <div style="height:0" scroll="true">${detail}</div>
                    </div>
                </div>
            </div>

            <footer class="position-fixed">
                <a class="btn">Map</a>
                <a class="btn">Tel</a>
                <a class="btn">Chat</a>
            </footer>

            <!-- 编译自动加入UI控件初始化代码 -->
            <script>
                require(
                    ['saber-ui', 'saber-ui-banner', 'saber-ui-suggest', 'saber-ui-list'], 
                    function (ui) 
                        ui.init(); 
                    }
                );
            </script>
        </body>
    </html>
```
