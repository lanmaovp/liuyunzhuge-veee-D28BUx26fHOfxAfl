
升讯威在线客服与营销系统是基于 .net core / WPF 开发的一款在线客服软件，宗旨是： **开放、开源、共享。努力打造 .net 社区的一款优秀开源产品。**


## 背景


随着下载私有化部署的用户越来越多，部署配置时的便利性到了需要进一步提升的时候。
部署时需要修改一些配置文件，除了直接手工修改外，之前我提供了一个 Windows 应用程序来自动化写入配置文件，但这有一些小的弊端，很多用户选择把文件上传到服务器上之后，直接在服务器上配置，而 Windows 界面程序无法在 Linux 、宝塔这样的环境中使用。用户还是只能手工修改配置文件，经常有修改错误的情况发生。
所以我提供了一个网页版本的配置文件生成工具，**直接在浏览器上使用，生成配置文件的内容，复制文件内容到终端的 vim 编辑器中直接粘贴即可**。高效，实用。


这个网页版小工具虽然功能并不复杂，然而开发它还是花了点时间，本文我将仔细讲解 **如何使用原生Web开发技术，来实现这样的功能。**


## 效果展示


[https://kf.shengxunwei.com/ServerSettingsTool/index.html](https://github.com)


### 在网页中填写服务器的各项配置信息：


![](https://img2024.cnblogs.com/blog/78019/202411/78019-20241122110140288-1697372789.png)


### 根据填写的配置信息，自动生成所需的配置文件内容：


![](https://img2024.cnblogs.com/blog/78019/202411/78019-20241122110158147-1525370926.png)


## 技术方案


下面我将详细介绍这样的网页版配置文件生成工具是如何开发的，在这个应用场景中，我使用了原生Web开发技术，使用了 Bootstrap \+ jQuery 的方案。


### Bootstrap


Bootstrap 是最受欢迎的前端框架之一，用于快速开发响应式、移动优先的网站和应用程序。它由 Twitter（现 X）开发并开源，为开发者提供了一系列预先设计好的 CSS 样式、HTML 模板、JavaScript 插件，能够节省大量的时间和精力，使网页设计更高效。


* Bootstrap 的核心优势是其响应式设计理念。它基于 CSS 媒体查询（Media Queries），能够根据不同设备的屏幕尺寸（如桌面显示器、平板电脑、手机）自动调整网页布局。例如，一个使用 Bootstrap 构建的网站，在大屏幕设备上可能会以多栏布局显示内容，而在小屏幕手机上则会自动堆叠成单栏布局，确保内容在任何设备上都能清晰可读。
* 它提供了一套响应式网格系统，将页面划分为 12 列。开发者可以通过指定不同的列数来分配内容在不同屏幕尺寸下的宽度。比如，在桌面端可以让一个元素占据 6 列（即屏幕宽度的一半），在移动端可以让它占据 12 列（全屏宽度）。


### jQuery


jQuery 是一个很实用的 JavaScript 库。


* 它的主要作用是让网页开发中的一些操作变得更简单。比如在操作网页元素方面，能很方便地找到页面中的各种元素，像按标签、ID 或者类名来选取。找到元素后，可以轻松地修改元素里的文字、属性和样式。
* 在事件处理上，给网页元素添加点击、鼠标移动、键盘按键等事件变得很容易，还能高效地处理动态添加元素的事件。
* 它也能制作动画，简单的显示隐藏动画或者复杂一点的位置、透明度变化等动画都可以实现。
* 另外，通过 jQuery 可以方便地发送异步请求，从服务器获取数据来更新网页的部分内容，而不用重新加载整个页面。


### highlight.js


Highlight.js 是一个用于在网页中对代码进行语法高亮显示的 JavaScript 库。它能够识别多种编程语言的语法，包括但不限于 JavaScript、Python、Java、C\+\+、HTML、CSS 等，使代码片段在网页上呈现出清晰、易读的格式，带有不同颜色来区分不同的语法元素。


* 它通过解析代码文本，根据各种编程语言的语法规则来识别关键字、变量、函数、注释等不同的语法成分。例如，在 JavaScript 代码中，它可以将function关键字标记为一种颜色，将变量名标记为另一种颜色。
* 当应用到网页时，通常需要在 HTML 中引入 Highlight.js 的库文件（JavaScript 和 CSS），然后指定要进行语法高亮显示的代码块，一般是通过类名或者其他属性来标识这些代码块。


### 开发核心功能


首先基于 Bootstrap 完成基本的页面开发，Bootstrap 提供了非常完善的布局功能，对于一个单页面的应用来说，这并不复杂，我们可以使用如下代码生成一个基本的页面结构，它包含了几个标签页：



```
<ul class="nav nav-tabs" id="myTab" role="tablist">
  <li class="nav-item" role="presentation">
    <button class="nav-link active" id="home-tab" data-bs-toggle="tab" data-bs-target="#home-tab-pane" type="button" role="tab" aria-controls="home-tab-pane" aria-selected="true">Homebutton>
  li>
  <li class="nav-item" role="presentation">
    <button class="nav-link" id="profile-tab" data-bs-toggle="tab" data-bs-target="#profile-tab-pane" type="button" role="tab" aria-controls="profile-tab-pane" aria-selected="false">Profilebutton>
  li>
  <li class="nav-item" role="presentation">
    <button class="nav-link" id="contact-tab" data-bs-toggle="tab" data-bs-target="#contact-tab-pane" type="button" role="tab" aria-controls="contact-tab-pane" aria-selected="false">Contactbutton>
  li>
  <li class="nav-item" role="presentation">
    <button class="nav-link" id="disabled-tab" data-bs-toggle="tab" data-bs-target="#disabled-tab-pane" type="button" role="tab" aria-controls="disabled-tab-pane" aria-selected="false" disabled>Disabledbutton>
  li>
ul>
<div class="tab-content" id="myTabContent">
  <div class="tab-pane fade show active" id="home-tab-pane" role="tabpanel" aria-labelledby="home-tab" tabindex="0">...div>
  <div class="tab-pane fade" id="profile-tab-pane" role="tabpanel" aria-labelledby="profile-tab" tabindex="0">...div>
  <div class="tab-pane fade" id="contact-tab-pane" role="tabpanel" aria-labelledby="contact-tab" tabindex="0">...div>
  <div class="tab-pane fade" id="disabled-tab-pane" role="tabpanel" aria-labelledby="disabled-tab" tabindex="0">...div>
div>

```

然后需要使用jQuery 来操作 form 表单，获取用户填写的配置信息：



```
  $(document).ready(function () {
      // 当按钮被点击时执行以下函数
      $('#btn').click(function () {
        // 获取输入框的值
        var inputValue = $('#inputBox').val();

        // 将获取到的值赋值给另一个变量（这里只是示例，可根据实际需求处理该值）
        var assignedValue = inputValue;

        // 将赋值后的变量值显示在指定的div元素中
        $('#result').text('获取并赋值后的值为：' + assignedValue);
      });
    });

```

最后，使用 highlight.js 来高亮显示生成的配置文件，以使它们更加可读：


在生成文件之后，调用 `hljs.highlightAll();` 方法即可。



```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/default.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js">script>


<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/languages/go.min.js">script>

<script>hljs.highlightAll();script>

```



---


### 简介下这个 .net 开发的小系统


可全天候 7 × 24 小时挂机运行，不掉线不丢消息，欢迎实测。



> [https://kf.shengxunwei.com/](https://github.com):[slower加速器](https://jisuanqi.org)


