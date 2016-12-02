---
title: web components是什么
tags:
  - web components
categories:
  - Frontend technology
toc: true
date: 2016-11-21 10:35:30
description: 
feature:
---

#### 简介
这篇文章简单介绍 WebComponent 标准，介绍哪些浏览器已经开始支持 WebComponents，讨论 WebComponents 能解决什么问题，以及它对 web 开发的重要性。你可以了解到如何利用 Vanilla javascript 编写一个简单的 WebComponent，我还会针对它的潜在优势分享我自己的一些拙见。

#### 什么是 web components ?
近年来，web 开发者们通过插件或者模块的形式在网上分享自己的代码，便于其他开发者们复用这些优秀的代码。同样的故事不断发生，人们不断的复用 javascript 文件，然后是 CSS 文件，当然还有 HTML 片段。但是你又必须祈祷这些引入的代码不会摧毁你的网站或者web app。WebComponents 是这类问题最好的良药，通过一种标准化的非侵入的方式封装一个组件，每个组件能组织好它自身的 HTML 结构、CSS 样式、javascript 代码，并且不会干扰页面上的其他代码。
<!-- more -->
#### The Shadow DOM
大家之前可能听说过 shadow DOM，但 shadow DOM 到底是什么？ 开发者能通过 shadow DOM 在文档流中创建一些完全独立于其他元素的子 DOM 树（sub-DOM trees）， 由于这个特性，使得我们可以封装一个具有独立功能的组件，并且可以保证不会在不无意中干扰到其它 DOM 元素。shadow DOM 和标准的 DOM 一样，可以设置它的样式，也可以用 javascript 操作它的行为。主文档流和基于 shadow DOM 创建的独立组件之间的互不干扰，所以组件的复用也就变得异常简单方便。

#### HTML 模板
只要你用过类似 Angular JS 之类的现代 javascript 框架，就一定对 HTML 模板再熟悉不过了。开发者通过模板来复用一些 HTML 代码段，在 HTML5 标准下我们甚至不需要 javascript 框架就能轻松使用模板。

#### 导入 HTML 模板
在模板中创建 HTML 代码块和子 DOM 树，使得我们可以用不同的物理文件来组织代码。通过<link>标签来引入这些文件，就像我们在 PHP 文件中引用 javascript 文件那样简单。

#### 自定义元素
我们声明一个语义化的自定义元素来引用组件，用 javascript 建立自定义元素和模板、shadow DOM 之间的关联，然后将自定义标签（例如<my-custom-element></my-custom-element>）插入到页面上就能得到一个封装好的组件。Angular JS 中有很多类似的写法。

#### 试写第一个 WebComponent
现在你应该已经对 WebComponents 有了一定的了解，但我想通过一个简单的例子能让你更好的理解上面那些枯燥的概念。以下代码展示了一个最简单的 WebComponent 由哪些元素组成，用<template>包裹 HTML 和 样式代码，用 javascript 将这些绑定到自定义标签 <favorite-colour>上。
``` html
<!-- WebComponent example based off element-boilerplate: https://github.com/webcomponents/element-boilerplate -->
<template>
    <style>
        .coloured {
            color: red;
        }
    </style>
    <p>My favorite colour is: <strong class="coloured">Red</strong></p>
</template>
<script>
    (function() {
        // Creates an object based in the HTML Element prototype
        var element = Object.create(HTMLElement.prototype);
        // Gets content from <template>
        var template = document.currentScript.ownerDocument.querySelector('template').content;
        // Fires when an instance of the element is created
        element.createdCallback = function() {
            // Creates the shadow root
            var shadowRoot = this.createShadowRoot();
            // Adds a template clone into shadow root
            var clone = document.importNode(template, true);
            shadowRoot.appendChild(clone);
        };
        document.registerElement('favorite-colour', {
            prototype: element
        });
    }());
</script>
```

用<link />引入 WebComponent 文件，并添加<favorite-colour>标签将 WebComponent 添加到页面上，如下代码所示：
``` html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>My First WebComponent</title>
    <link rel="import" href="components/favorite-colour.html" />
</head>
<body>
    <favorite-colour></favorite-colour>
</body>
</html>
```
这个简单的例子演示了如何创建一个易复用、可维护的 WebComponent。

#### WebComponent 兼容性
你可能会说：“确实很diao，但尼玛什么时候才能 '真正' 用得上呢？”。我会告诉你：“现在就能用了少年”。下面的表格列出了主流浏览器对 WebComponent 中各个特性的兼容性。 Web Components 是什么？它为什么对我们这么重要？ 意料之中的是基于 Blink 的浏览器在这方面处于领先地位。上表清晰的告诉我们，哪怕是最简单的 WebComponent 例子也必须用 Chrome 或 Opera 运行。

#### 填补空缺
虽然大部分浏览器还不支持 WebComponent ，但是有个叫做 webcomponentsjs 的兼容库，可以让 WebComponent 在不支持它的浏览器上运行起来。只要你在项目中引入这个库，就可以像上面的例子那样将 WebComponents 用起来。

#### WebComponents 的重要性
WebComponents 将如何改变当前的 Web 开发模式？本文的开头已经给出了答案，“通过一种标准化的非侵入的方式封装一个组件”，但这究竟能带来哪些好处呢？

#### 无害插件
本文的前面已经介绍了开发者可以通过 shadow DOM 创建子 DOM 树，并且不会被页面上的 CSS 样式和 javascript 脚本所影响。显而易见的好处就是当你引入一个第三方组件的时候，不用担心它会对你的网站其他功能造成影响。对于开发者来说，开发无害插件变得更简单了。下面的例子用刚才写的 WebComponent 展示了这种封装的独立性。在 WebComponent 内部定义了一个名为colour的类，并将color属性设置为 red 。在主页面中colour类的color为 green 并被设为!important，你会发现在 WebComponent 中的颜色还是展示为红色。 你可以访问 Github 获取示例代码。

#### 一劳永逸
标准的目的是增强通用性。一旦 WebComponents 被广泛支持起来，我们就能开发更通用的组件，而不用考虑其他项目用的是什么技术。再也不用针对 jQuery 写插件，再也不用为 Angular JS 写 directives，再也不用为 Ember.js 写 addons。 一劳永逸，是 WebComponents 带来的最大好处。作为一个全职的 Angular JS 开发者，经常需要将 jQuery 插件翻译成 directives，然后才能在我的项目里用起来，这些工作非常繁琐。程序员不应该局限于某一种前端框架，但现实情况是我们正在被一个个前端框架所限制，因为不同框架的代码不能共享。WebComponents 能将我们从水深火热之中解救出来。

#### 维护与测试
通过这样的标准编写的组件具有更好的可维护性。最佳实践能够更快的被采用，并给我们带来更快更可靠的 Web 应用。测试会变得更简单，测试规范也能随着组件一起发布。

#### Abstraction
我们可以在 WebComponents 里开发复杂的功能，就可以将较少的精力耗费在开发复杂 Web 应用上了。你只需要将这些组件组装起来，保证他们之间能够互相通信，就能组装出一个完整应用。以 Angular JS 1.x 为例，不需要写 controllers，不需要写 directives，不需要写那么多的 scope，只要提供一些基本的服务和路由就行。当然 Angular 2.0 已经将 WebComponents 规划进去了。

#### HTML 的故事
HTML 5 规范带来了一些新的语义化标签，例如<section>,nav。这以为着不用详细阅读代码细节就能了解开发者的意图。WebComponents 将彻底改变我们使用 HTML 的方式，在组件的 HTML 代码层面，自定义元素和属性能表达更多语义。如下面的例子所示：

#### 传统的 HTML 写法
``` html
<!-- PAGE NAVIGATION -->
<div>
    <ul>
        <li>Home</li>
        <li>About</li>
        <li>Contact</li>
    </ul>
</div>
<!-- CONTENT AREA -->
<div>
    <p>Here is some simple content in the content area.</p>
</div>
<!-- GALLERY -->
<div>
    <img src="animage1.png" />
    <img src="animage2.png" />
    <img src="animage3.png" />
    <img src="animage4.png" />
    <img src="animage5.png" />
</div>
<!-- FOOTER -->
<div>
    <p>A simple footer</p>
</div>
```

#### WebComponents 的语义化写法
``` html
<page-navigation data-position="top"></page-navigation>
<content data-theme="dark">
    <p>Here is some simple content in the content area.</p>
</content>
<image-gallery data-fullscreen="true">
    <img src="animage1.png" />
    <img src="animage2.png" />
    <img src="animage3.png" />
    <img src="animage4.png" />
    <img src="animage5.png" />
</image-gallery>
<footer>
    <p>A simple footer</p>
</footer>
```

虽然上述例子比较简单，我们还是能从中看出两者的显著区别。第一个例子使用标准的 HTML 标签，很难直接从代码看出最终的渲染结果，而第二个例子使用了 HTML5 标签和自定义标签，从代码层面提供了更多有用信息。从这些具有语义的标签就能很快理解页面每一个区块的含义，例如page-navigation和footer。其它信息可以通过自定义属性传递，例如data-fullscreen和data-position这样的属性就很好的描述了它将传递给页面什么数据。

#### 另一个大坑
上述的那些优点能让 Web 开发变得更美好，希望你跟我一样激动并满怀期待。但是...也有可能带来一些问题，历史一次次证明 Web 标准的实际应用可能会分裂为多个分支，给我们带来艰难的抉择，我担心这样的事情也会发生在 WebComponents 身上。 Web Components 是什么？它为什么对我们这么重要？ 目前已经有三个基于 WebComponent 标准的框架，并且都很好的兼容低级浏览器。这本来是件好事，但也意味着我们开发 WebComponents 应用时有三个选择：X-Tag, Polymer, Bosonic。既然都是支持 WebComponent 标准，那么基于 Polymer 开发的组件代码能够用在 x-tag 组件里吗？反过来呢？看看下面的例子。

#### X-Tag 组件 (源码)
``` html
<!-- Import X-Tag -->
<script src="../bower_components/x-tag-core/src/core.js"></script>
<template>
 <p>Hello <strong></strong> :)</p>
</template>
<script>
 (function(window, document, undefined) {
     // Refers to the "importer", which is index.html
     var thatDoc = document;
     // Refers to the "importee", which is src/hello-world.html
     var thisDoc = document._currentScript.ownerDocument;
     // Gets content from <template>
     var template = thisDoc.querySelector('template').content;
     xtag.register('hello-world', {
         lifecycle: {
             created: function() {
                 // Caches <strong> DOM query
                 this.strong = template.querySelector('strong');
                 // Creates the shadow root
                 this.shadowRoot = this.createShadowRoot();
                 this.uiSetWho();
             },
             attributeChanged: function() {
                 this.uiSetWho();
             }
         },
         accessors: {
             who: {
                 attribute: {},
                 get: function(){
                     return this.getAttribute('who') || "World"
                 },
                 set: function(value){
                     this.xtag.data.who = value;
                 }
             }
         },
         methods: {
             uiSetWho: function() {
                 // Sets "who" value into <strong>
                 this.strong.textContent = this.who;
                 // Removes shadow root content
                 this.shadowRoot.innerHTML = '';
                 // Adds a template clone into shadow root
                 var clone = thatDoc.importNode(template, true);
                 this.shadowRoot.appendChild(clone);
             }
         }
     });
 })(window, document);
</script>
```

#### Polymer 组件 (源码)
``` html
<!-- Import Polymer -->
<link rel="import" href="../bower_components/polymer/polymer.html">
<!-- Define your custom element -->
<polymer-element name="hello-world" attributes="who">
 <template>
     <p>Hello <strong>{{who}}</strong> :)</p>
 </template>
 <script>
     Polymer('hello-world', {
         who: 'World'
     });
 </script>
</polymer-element>
```
等等，看起来不对劲啊！我承认我还没有真正这两个框架开发过，有没有人能告诉我是在杞人忧天，但这看起来就真的是在用两种完全不兼容的方式开发 WebComponents。

我对 Angular 2 也有同样的担心，他们声称完全支持 WebComponent 标准，但显然还会有很多框架层面的东西。Angular 团队应该很清楚该怎么做并且为什么这么做，希望利大于弊吧。如果 Angular 团队成员能看到这篇文章，并向大家介绍 WebComponents 在 Angular 2 中的用法，那就再好不过了。

前面提到了语义化 HTML 标签的好处，我们通过阅读代码就能快速理解它的含义。当然这还取决于开发者是否使用语义化标签和语义化的自定义属性做开发。更重要的是，社区应尽快提供优秀的最佳实践来引导普通开发者形成更好的习惯。

#### 总结
WebComponents 能彻底改变 Web 开发，但还需时日。前端社区需要做大量工作才能使 WebComponents 变得真正可用，才能让大家享受到组件式 Web 开发的便利。

你可以在 WebComponents.org 这个网站了解更多关于 WebComponents 的知识。他们的 GitHub 账号 里有很多适合学习的例子，本文的例子也来自其中。

我会很乐意听到你们对本文的评论和对 WebComponent 的见解。

转自：http://sentsin.com/web/1089.html