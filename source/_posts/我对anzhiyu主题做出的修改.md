---
title: 我对anzhiyu主题做出的修改
date: 2025-11-15 00:00:01
tags: [主题修改, 技术记录]
---

# 我对anzhiyu主题做出的修改

## 1.太极加载画面修改记录

### 1.1. 太极图标添加与样式设置

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -50,3 +50,38 @@ @keyframes loadingAction
     100% {
         opacity: .4;
     }
+
+  // 添加太极图样式
+  .taichi-spinner
+    position: fixed;
+    top: 70%;
+    left: 50%;
+    transform: translate(-50%, -50%);
+    width: 80px;
+    height: 80px;
+    z-index: 1002;
+    animation: spin 2s linear infinite;
+    background-image: url(/img/taichi.svg);
+    background-size: contain;
+    background-repeat: no-repeat;
+    transition: opacity 0.2s;
+
+  // 加载完成时隐藏太极图
+  #loading-box.loaded .taichi-spinner,
+  body.pace-done .taichi-spinner
+    opacity: 0;
+    z-index: -1000;
+    animation-play-state: paused;
+
+  @keyframes spin
+    0% {
+        transform: translate(-50%, -50%) rotate(0deg);
+    }
+    100% {
+        transform: translate(-50%, -50%) rotate(360deg);
+    }
```

**修改文件**: `themes/anzhiyu/layout/includes/loading/pace.pug`

```diff
@@ -1,2 +1,3 @@
 link(rel="stylesheet", href=url_for(theme.preloader.pace_css_url || theme.asset.pace_default_css))
 script(async src=url_for(theme.asset.pace_js), data-pace-options='{ "restartOnRequestAfter":false,"eventLag":false}')
+.taichi-spinner
```

### 1.2. 修改效果

1. 页面加载时，太极图标会在进度条下方居中显示（top: 70%位置）
2. 太极图标以2秒为周期持续旋转
3. 页面加载完成后，太极图标会平滑淡出并停止旋转
4. 解决了与头像重叠的问题
5. 解决了主题更新后只闪一下就消失的问题
6. 太极图会与头像同时消失，保持视觉一致性

## 2. 将网站中md的代码展示字体改为Convergence

### 2.1 添加Convergence字体文件

**修改文件**: 新增字体文件 `themes/anzhiyu/source/fonts/Convergence.ttf`

```diff
+ themes/anzhiyu/source/fonts/Convergence.ttf
```

### 2.2 修改CSS配置文件

**修改文件**: `themes/anzhiyu/source/css/var.styl`

```diff
@@ -18,8 +18,14 @@ $theme-toc-color = $themeColorEnable && hexo-config('theme_color.toc_color') ? c
 
 // font
+@font-face
+  font-family 'Convergence'
+  src url('/fonts/Convergence.ttf') format('truetype')
+  font-weight normal
+  font-style normal
+
 $dafault-font-family = -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Lato, Roboto, 'PingFang SC', 'Microsoft YaHei', sans-serif
-$dafault-code-font = consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', sans-serif
+$dafault-code-font = 'Convergence', consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', sans-serif
 $font-family = hexo-config('font.font-family') ? unquote(hexo-config('font.font-family')) : $dafault-font-family
 $code-font-family = hexo-config('font.code-font-family') ? unquote(hexo-config('font.code-font-family')) : $dafault-code-font
 $site-name-font = hexo-config('blog_title_font.font-family') && unquote(hexo-config('blog_title_font.font-family'))
```

### 2.3 修改主题配置文件

**修改文件**: `_config.anzhiyu.yml`

```diff
@@ -736,7 +736,7 @@ font:
   global-font-size: 16px
   code-font-size:
   font-family:
-  code-font-family: consolas, Menlo, "PingFang SC", "Microsoft JhengHei", "Microsoft YaHei", sans-serif
+  code-font-family: "Convergence", consolas, Menlo, "PingFang SC", "Microsoft JhengHei", "Microsoft YaHei", sans-serif

 # Font settings for the site title and site subtitle
 # 左上角网站名字 主页居中网站名字
```

### 2.4 修改效果

1. 网站中所有Markdown代码块的字体已改为Convergence
2. 字体文件已添加到主题资源目录
3. 配置文件已更新，确保Convergence字体作为代码字体首选

## 3. 在加载页底部添加名言显示

### 3.1 修改fullpage-loading.pug文件

**修改文件**: `themes/anzhiyu/layout/includes/loading/fullpage-loading.pug`

```diff
@@ -1,17 +1,43 @@
 - loading_img = theme.preloader.avatar ? theme.preloader.avatar : theme.avatar.img
 #loading-box(onclick='document.getElementById("loading-box").classList.add("loaded")')
   .loading-bg
     img.loading-img(alt="加载头像" class='nolazyload' src=url_for(loading_img))
     .loading-image-dot
+  .loading-quote
+    .quote-content
+    .quote-author
 script.
+  // 获取名言
+  async function fetchQuote() {
+    try {
+      // 使用CORS代理或者直接请求
+      const response = await fetch('https://uapis.cn/api/v1/saying', {
+        method: 'GET',
+        headers: {
+          'Content-Type': 'application/json',
+        },
+        mode: 'cors'
+      });
+      const data = await response.json();
+      console.log('Quote API response:', data);
+      if (data.code === 200 && data.data) {
+        document.querySelector('.quote-content').textContent = data.data.content;
+        document.querySelector('.quote-author').textContent = `— ${data.data.author}`;
+      } else {
+        // 如果API返回错误，使用默认名言
+        document.querySelector('.quote-content').textContent = '加载中...';
+        document.querySelector('.quote-author').textContent = '';
+      }
+    } catch (error) {
+      console.error('Failed to fetch quote:', error);
+      // 加载失败时显示默认名言
+      document.querySelector('.quote-content').textContent = '道可道，非常道；名可名，非常名。';
+      document.querySelector('.quote-author').textContent = '— 《道德经》';
+    }
+  }
+
+  // 初始化名言
+  fetchQuote();
+  
   const preloader = {
     endLoading: () => {
       document.getElementById('loading-box').classList.add("loaded");
     },
     initLoading: () => {
       document.getElementById('loading-box').classList.remove("loaded")
     }
   }
   window.addEventListener('load',()=> { preloader.endLoading() })
   setTimeout(function(){preloader.endLoading();},10000)

   if (!{theme.pjax && theme.pjax.enable}) {
     document.addEventListener('pjax:send', () => { preloader.initLoading() })
     document.addEventListener('pjax:complete', () => { preloader.endLoading() })
   }
```

### 3.2 修改pace.pug文件

**修改文件**: `themes/anzhiyu/layout/includes/loading/pace.pug`

```diff
@@ -1,3 +1,25 @@
 link(rel="stylesheet", href=url_for(theme.preloader.pace_css_url || theme.asset.pace_default_css))
 script(async src=url_for(theme.asset.pace_js), data-pace-options='{ "restartOnRequestAfter":false,"eventLag":false}')
 .taichi-spinner
+.loading-quote
+  .quote-content
+  .quote-author
+script.
+  // 获取名言
+  async function fetchQuote() {
+    try {
+      // 使用CORS代理或者直接请求
+      const response = await fetch('https://uapis.cn/api/v1/saying', {
+        method: 'GET',
+        headers: {
+          'Content-Type': 'application/json',
+        },
+        mode: 'cors'
+      });
+      const data = await response.json();
+      console.log('Quote API response:', data);
+      if (data.code === 200 && data.data) {
+        document.querySelector('.quote-content').textContent = data.data.content;
+        document.querySelector('.quote-author').textContent = `— ${data.data.author}`;
+      } else {
+        // 如果API返回错误，使用默认名言
+        document.querySelector('.quote-content').textContent = '加载中...';
+        document.querySelector('.quote-author').textContent = '';
+      }
+    } catch (error) {
+      console.error('Failed to fetch quote:', error);
+      // 加载失败时显示默认名言
+      document.querySelector('.quote-content').textContent = '道可道，非常道；名可名，非常名。';
+      document.querySelector('.quote-author').textContent = '— 《道德经》';
+    }
+  }
+
+  // 初始化名言
+  fetchQuote();
```

### 3.3 修改loading.styl文件

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -64,7 +64,9 @@
     background-repeat: no-repeat;
     transition: opacity 0.2s;

-  body.pace-done .taichi-spinner
+  // 加载完成时隐藏太极图
+  #loading-box.loaded .taichi-spinner,
+  body.pace-done .taichi-spinner
     opacity: 0;
     z-index: -1000;
     animation-play-state: paused;
@@ -71,3 +73,31 @@
     0% {
         transform: translate(-50%, -50%) rotate(0deg);
     }
     100% {
         transform: translate(-50%, -50%) rotate(360deg);
     }
+
+  // 名言样式
+  .loading-quote
+    position: fixed;
+    bottom: 15%; // 底部10-20%的位置
+    left: 50%;
+    transform: translateX(-50%);
+    width: 80%;
+    max-width: 800px;
+    z-index: 1002;
+    text-align: center;
+    transition: opacity 0.2s;
+    
+    .quote-content
+      font-size: 18px;
+      font-weight: 500;
+      color: var(--anzhiyu-fontcolor);
+      margin-bottom: 8px;
+      line-height: 1.6;
+      
+    .quote-author
+      font-size: 14px;
+      color: var(--anzhiyu-fontcolor);
+      opacity: 0.8;
+
+  // 加载完成时隐藏名言
+  #loading-box.loaded .loading-quote,
+  body.pace-done .loading-quote
+    opacity: 0;
+    z-index: -1000;
```

### 3.4 修改效果

1. 在加载页底部10-20%的位置添加了名言显示功能
2. 名言内容从uapis.cn/api/v1/saying API获取
3. 支持两种加载方式（fullpage和pace）
4. 加载完成后自动隐藏名言
5. 响应式设计，适配不同屏幕尺寸
6. 太极图不再过早消失，会与头像同时隐藏
7. 名言显示功能稳定，添加了CORS支持和错误处理
8. 即使API请求失败也能显示默认名言

## 4. 修改搜索输入框样式

### 4.1 实现搜索框浮动标签效果

**修改文件**: `themes/anzhiyu/source/css/_search/algolia.styl`

```diff
@@ -3,14 +3,46 @@
     .ais-SearchBox
-      input
-        padding: .5rem 1rem
+      position: relative
+      margin: 0 auto
+      max-width: 100%
+      width: 100%
+
+      input
+        padding: 5px 14px
         width: 100%
         outline: none
-        border: var(--style-border)
-        border-radius: var(--anzhiyu-radius)
-        background: var(--anzhiyu-secondbg)
-        color: var(--search-input-color)
-        &:focus
-          border: var(--style-border-hover)
+        border: 2px solid $search-color
+        border-radius: 40px
+        background: var(--search-bg)
+        color: var(--search-input-color)
+        -webkit-appearance: none
+        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1)
+        &:focus,
+        &:not(:placeholder-shown)
+          border-color: #2196f3
+          box-shadow: 0 0 0 4px rgba(33, 150, 243, 0.1)
+      
+      .form-label
+        position: absolute
+        left: 18px
+        top: 50%
+        transform: translateY(-50%)
+        color: var(--search-input-color)
+        font-size: 14px
+        pointer-events: none
+        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1)
+        background-color: transparent
+        padding: 0
+        white-space: nowrap
+        opacity: 0.7
+      
+      input:focus + .form-label,
+      input:not(:placeholder-shown) + .form-label
+        transform: translateY(-100%) scale(0.8)
+        top: 5px
+        color: #2196f3
+        font-weight: 500
+        opacity: 1
```

**修改文件**: `themes/anzhiyu/source/css/_search/local-search.styl`

```diff
@@ -1,17 +1,41 @@
 #local-search
   .search-dialog
     .local-search-box
       margin: 0 auto
       max-width: 100%
       width: 100%
+      position: relative

       input
         padding: 5px 14px
         width: 100%
         outline: none
         border: 2px solid $search-color
+        border-radius: 40px
         background: var(--search-bg)
         color: var(--search-input-color)
+        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1)
         -webkit-appearance: none
+        &:focus,
+        &:not(:placeholder-shown)
+          border-color: #2196f3
+          box-shadow: 0 0 0 4px rgba(33, 150, 243, 0.1)
+      
+      .form-label
+        position: absolute
+        left: 18px
+        top: 50%
+        transform: translateY(-50%)
+        color: var(--search-input-color)
+        font-size: 14px
+        pointer-events: none
+        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1)
+        background-color: transparent
+        padding: 0
+        white-space: nowrap
+        opacity: 0.7
+      
+      input:focus + .form-label,
+      input:not(:placeholder-shown) + .form-label
+        transform: translateY(-100%) scale(0.8)
+        top: 5px
+        color: #2196f3
+        font-weight: 500
+        opacity: 1
```

**修改文件**: `themes/anzhiyu/layout/includes/third-party/search/local-search.pug`

```diff
@@ -14,7 +14,7 @@

     .search-wrap
       #local-search-input
         .local-search-box
-          input(placeholder=_p("search.local_search.input_placeholder") type="text").local-search-box--input
+          input(placeholder=" " type="text").local-search-box--input
+          label.form-label= _p("search.local_search.input_placeholder")
       hr
       #local-search-results
```

### 4.2 修改效果

1. 保持了原有的输入框大小，对不同屏幕做了适配
2. 本地搜索输入框实现了文字上浮功能
3. 输入框聚焦或有内容时，标签会上浮到输入框上方
4. 应用了平滑的过渡动画，提升用户体验
5. 保持了原有的响应式设计，适配不同屏幕尺寸
6. 增强了输入框的视觉反馈，聚焦时显示蓝色边框和阴影

这些修改提升了网站的搜索体验，使搜索输入框更加现代化、美观，同时保持了良好的可用性和响应式设计。
