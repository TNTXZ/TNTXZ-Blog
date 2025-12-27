---
title: 我对anzhiyu主题做出的修改
date: 2025-11-15 00:00:01
tags: [主题修改, 技术记录]
---

# 我对anzhiyu主题做出的修改

## 1.太极加载画面修改记录

### 1.1. 添加太极图标到加载进度条下方

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -50,3 +50,24 @@ @keyframes loadingAction
     100% {
         opacity: .4;
     }
+
+  // 添加太极图样式
+  .taichi-spinner
+    position: fixed;
+    top: 30%;
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

### 1.2. 调整太极图标位置避免与头像重叠

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -54,7 +54,7 @@
 
   .taichi-spinner
     position: fixed;
-    top: calc(50% + 20px);
+    top: 70%;
     left: 50%;
     transform: translate(-50%, -50%);
     width: 80px;
```

### 1.3. 修复太极图标加载结束后继续旋转的问题

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -66,4 +66,5 @@
   body.pace-done .taichi-spinner,
   #loading-box.loaded ~ .taichi-spinner
     opacity: 0;
     z-index: -1000;
+    animation-play-state: paused;
```

### 1.4. 修复主题更新后太极图标只闪一下的问题

**修改文件**: `themes/anzhiyu/source/css/_global/loading.styl`

```diff
@@ -66,6 +66,4 @@
   body.pace-done .taichi-spinner,
-  #loading-box.loaded ~ .taichi-spinner
     opacity: 0;
     z-index: -1000;
     animation-play-state: paused;
```

### 1.5. 修改效果

1. 页面加载时，太极图标会在进度条下方居中显示
2. 太极图标以2秒为周期持续旋转
3. 页面加载完成后，太极图标会平滑淡出并停止旋转
4. 解决了与头像重叠的问题
5. 解决了主题更新后只闪一下就消失的问题

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
+$@font-face
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