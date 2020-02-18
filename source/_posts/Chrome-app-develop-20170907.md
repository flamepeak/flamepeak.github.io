---
title: Chrome 插件 开发学习
date: 2017-09-07 08:26:58
tags: [Chrome]
categories: [Web前端]
---

360浏览器已经把Chrome插件开发文档翻译的差不多了：  
[English](https://developer.chrome.com/extensions)
[中文](http://open.chrome.360.cn/extension_dev/overview.html)

### 基本概念

一个应用（扩展）其实是压缩在一起的一组文件，包括HTML，CSS，Javascript脚本，图片文件，还有其它任何需要的文件。 应用（扩展）本质上来说就是web页面，它们可以使用所有的浏览器提供的API，从XMLHttpRequest到JSON到HTML5全都有。

应用（扩展）可以与Web页面交互，或者通过`content script`或`cross-origin XMLHttpRequests`与服务器交互。应用（扩展）还可以访问浏览器提供的内部功能，例如标签或书签等。


**每个应用（扩展）都应该包含下面的文件：**

> 一个manifest文件  
  一个或多个html文件（除非这个应用是一个皮肤）  
  可选的一个或多个javascript文件  
  可选的任何需要的其他文件，例如图片

在开发应用（扩展）时，需要把这些文件都放到同一个目录下。发布应用（扩展）时，这个目录全部打包到一个应用（扩展）名是.crx的压缩文件中。如果使用Chrome Developer Dashboard,上传应用（扩展），可以自动生成.crx文件。


**基本架构**

绝大多数应用（扩展）都包含一个背景页面(background page)，用来执行应用（扩展）的主要功能。

背景页面并不是应用（扩展）中唯一的页面。例如，一个browser action可以包含一个弹窗(popup)，而弹窗就是用html页面实现的。应用（扩展）还可以使用chrome.tabs.create()或者window.open()来显示内部的HTML文件。  
应用（扩展）里面的HTML页面可以互相访问各自DOM树中的全部元素，或者互相调用其中的函数。

**Content scripts**

如果一个应用（扩展）需要与web页面交互，那么就需要使用一个`content script`。Content script脚本是指能够在浏览器已经加载的页面内部运行的javascript脚本。可以将`content script`看作是网页的一部分，而不是它所在的应用（扩展）的一部分。  
Content script可以获得浏览器所访问的web页面的详细信息，并可以对页面做出修改。

Content script与它所在的应用（扩展）并不是完全没有联系。一个content script脚本可以与所在的应用（扩展）交换消息.

**隐身模式**

要检查窗口是否在隐身模式下，检查Tab或Window对象的incognito属性。例如：

```javascript
var bgPage = chrome.extension.getBackgroundPage();

function saveTabData(tab, data) {
if (tab.incognito) {
bgPage[tab.url] = data; // Persist data ONLY in memory
} else {
localStorage[tab.url] = data; // OK to store data
}
```

**典型的manifest.json**

```json
{
  // Required
  "manifest_version": 2,
  "name": "My Extension",
  "version": "versionString",

  // Recommended
  "default_locale": "en",  //Specifies the subdirectory of _locales that contains the default strings for this extension. This field is required in extensions that have a _locales directory; it must be absent in extensions that have no _locales directory. 
  "description": "A plain text description",
  "icons": {...},  //One or more icons that represent the extension, app, or theme.

  // Pick one (or none)
  "browser_action": {...}, //Use browser actions to put icons in the main Google Chrome toolbar, to the right of the address bar. In addition to its icon, a browser action can also have a tooltip, a badge, and a popup.
  "page_action": {...},  //Use the chrome.pageAction API to put icons in the main Google Chrome toolbar, to the right of the address bar. Page actions represent actions that can be taken on the current page, but that aren't applicable to all pages. Page actions appear grayed out when inactive. 

  // Optional
  "author": ...,
  "automation": ...,
  "background": {  //A common need for extensions is to have a single long-running script to manage some task or state. Background pages to the rescue. It exists for the lifetime of your extension, and only one instance of it at a time is active.
    // Recommended
    "persistent": false
  },
  "background_page": ...,
  "chrome_settings_overrides": {...},
  "chrome_ui_overrides": {
    "bookmarks_ui": {
      "remove_bookmark_shortcut": true,
      "remove_button": true
    }
  },
  "chrome_url_overrides": {...},
  "commands": {...},
  "content_capabilities": ...,
  "content_scripts": [{...}],
  "content_security_policy": "policyString",
  "converted_from_user_script": ...,
  "current_locale": ...,
  "devtools_page": "devtools.html",
  "event_rules": [{...}],
  "externally_connectable": {
    "matches": ["*://*.example.com/*"]
  },
  "file_browser_handlers": [...],
  "file_system_provider_capabilities": {
    "configurable": true,
    "multiple_mounts": true,
    "source": "network"
  },
  "homepage_url": "http://path/to/homepage",
  "import": [{"id": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}],
  "incognito": "spanning, split, or not_allowed",
  "input_components": ...,
  "key": "publicKey",
  "minimum_chrome_version": "versionString",
  "nacl_modules": [...],
  "oauth2": ...,
  "offline_enabled": true,
  "omnibox": {
    "keyword": "aString"
  },
  "optional_permissions": ["tabs"],
  "options_page": "options.html",
  "options_ui": {
    "chrome_style": true,
    "page": "options.html"
  },
  "permissions": ["tabs"],  //To use most chrome.* APIs, your extension or app must declare its intent in the "permissions" field of the manifest.
  "platforms": ...,
  "plugins": [...],
  "requirements": {...},
  "sandbox": [...],
  "short_name": "Short Name",
  "signature": ...,
  "spellcheck": ...,
  "storage": {
    "managed_schema": "schema.json"
  },
  "system_indicator": ...,
  "tts_engine": {...},
  "update_url": "http://path/to/updateInfo.xml",
  "version_name": "aString",
  "web_accessible_resources": [...]
}
```

**popup.html**

```html
<!doctype html>
<html>
  <head>
    <title>Getting Started Extension's Popup</title>
    <style>
      body {
        min-width: 357px;
        overflow-x: hidden;
      }

      img {
        margin: 5px;
        border: 2px solid black;
        vertical-align: middle;
        width: 75px;
        height: 75px;
      }
    </style>

    <!--
      - JavaScript and HTML must be in separate files: see our Content Security
      - Policy documentation[1] for details and explanation.
      -
      - [1]: http://developer.chrome.com/extensions/contentSecurityPolicy.html
     -->
    <script src="popup.js"></script>
  </head>
  <body>
  </body>
</html>
```




### Manifest Version

Error:The 'manifest_version' key must be present and set to 2 (without quotes)

官方文档这样说： Developers should specify which version of the manifest specification their package targets by setting a `manifest_version` key in their manifests.

Developers should currently specify `'manifest_version': 2:`

```json
{
  ...,
  "manifest_version": 2,
  ...
}
```
