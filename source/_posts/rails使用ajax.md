---
title: rails使用ajax
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-08-18 11:42:08
keywords:
  - rails
  - ajax
description:
  - rails
  - ajax
feature:
---

>It’s not a bug - it’s an undocumented feature. - Unknown

#### 什麼是 Ajax?
Ajax 是 Asynchronous JavaScript and XML的縮寫，是一種不需要重新整理頁面，透過 JavaScript 來與伺服器交換資料、更新網頁內容的技術。目的在於改善使用者的操作介面，提昇流暢度。它主要是透過瀏覽器提供的XMLHttpRequestObject來達成，不過因為跨瀏覽器的困難度，大多數人們會選擇使用 JavaScript Library 來處理 Ajax，例如 JQuery。

雖然Ajax的縮寫中包括XML，但是實務上並不一定要用XML格式，事實上也已經很少人使用XML當作傳輸的格式了。總歸來說，依照Ajax使用的格式分類，有三種方式：

* 向伺服器請求 HTML 片段，然後客戶端瀏覽器上的 JavaScript 再替換掉頁面上的元素
* 向伺服器請求 JavaScript 程式腳本，然後客戶端瀏覽器執行它
* 向伺服器請求 JSON 或 XML 資料格式，然後客戶端瀏覽器的 JavaScript 解析後再動作。
<!-- more -->
第一種方式非常簡單，但是限制是一次只能更新一小塊內容。

Rails 預設使用第二種方式，一方面程式撰寫較容易，另一方面也是貫徹 Rails 將 template 統一在 server-side rendering 的設計哲學。

第三種方式則將 JavaScript 程式都放在客戶端瀏覽器上，相較於第二種則多了解析 JSON 或 XML 的部份。以Web API的設計角度來看，與表現層無關的JSON格式是比較乾淨的，可以獲得比較好的重複使用性。如果團隊中有專門的前端工程師，他們會比較喜歡這種方式。

#### Unobtrusive JavaScript
Rails 內建使用的JavaScript使用一種叫做Unobtrusive JavaScript(UJS)方式。什麼是Unobtrusive呢？用個範例來說吧，它會將超連結改成用表單DELETE送出，並且用一個提示視窗來作確認：
``` erb
link_to 'Remove', event_path(1), :method => :delete, :data => { :confirm => "Sure?" }
```
在Rails 3以前的版本，會輸出：
``` html
<a onclick="if (confirm('Sure?')) { var f = document.createElement('form'); f.style.display = 'none'; this.parentNode.appendChild(f); f.method = 'POST'; f.action = this.href;var m = document.createElement('input'); m.setAttribute('type', 'hidden'); m.setAttribute('name', '_method'); m.setAttribute('value', 'delete'); f.appendChild(m);f.submit(); };return false;" href="/events/1">Remove</a>
```
在Rails 3之後，會輸出：
``` html
<a rel="nofollow" data-confirm="Sure?" data-method="delete" class="delete" href="/events/1">Remove</a>
```
Unobtrusive也就是將JavaScript程式與HTML完全分開，除了可以讓HTML碼乾淨之外，也可以支援更換不同的JavaScript Library，例如把Rails內建的jQuery換成Protytype.js或angular.js等等。

>在Layout中有輸出一段<%= csrf_meta_tag %>的作用就是搭配給UJS使用的，讓JavaScript可以拿到CSRF安全驗證碼，我們會在安全一章討論到什麼是CSRF。

#### 第一種方式：替換 HTML 片段
編輯 app/views/events/index.html.erb 最下方加入：
``` erb
<%= link_to 'Hello!', welcome_say_hello_path, :id => "ajax-load"  %>

<div id="content">
</div>

<script>
$('#ajax-load').click( function(e){
  e.preventDefault();
  var url =  $(this).attr("href");
  $.ajax(url, {
    success: function(response) {
      $("#content").html(response);
    }
  });
</script>
```
如此點下超連結後，就會把回傳的HTML置入到<div id="content">裡面。

#### 第二種方式：使用 JavaScript 腳本
編輯 app/views/events/index.html.erb，在迴圈中間加入
``` erb
<%= link_to 'ajax show', event_path(event), :remote => true %>
```
在迴圈外插入一個<div id="event_area"></div>

編輯 app/controllers/events_controller.rb，在 show action 中加入
``` erb
respond_to do |format|
  format.html
  format.js
end
```
新增 app/views/events/_event.html.erb，內容與 show.html.erb 相同

新增 app/views/events/show.js.erb，內容如下
``` erb
$('#event_area').html("<%= escape_javascript(render :partial => 'event') %>")
             .css({ backgroundColor: '#ffff99' });
```
瀏覽 http://localhost:3000/events

>escape_javascript()可以縮寫為j()。

上述 :remote => true 背後的原理，如同以下的 jQuery 程式碼：
``` js
$("#ajaxscript").click(function(e){
  e.preventDefault();
  var url =  $(this).attr("href");
  $.ajax(url, {
    dataType: "script"
  })
})
```
你會發現到這段程式碼其實非常一般化，這也是為什麼 Rails 可以將它變成 :remote => true 的原因。

#### Ajax 按鈕
除了超連結 link_to之外，按鈕 button_to 加上:remote => true參數也會變成 Ajax。
``` erb
button_to "Remove",event_path(@event)
```
除了已經看過的 :data => { :confirm => "Are you Sure?" }之外，disable_with可以避免使用者連續按下送出：
``` erb
button_to "Remove", event_path(@event), :data => { :disable_with => 'Removing' }
```
#### Ajax 表單
除了超連結 link_to 加上 :remote 可以變成 Ajax 之外，表單 form_for 也可以加上:remote變成 Ajax。
``` erb
form_for @event, :remote => true
```

#### 第三種方式：使用 JSON 資料格式
JavaScript Object Notation(JSON)是一種源自JavaScript的資料格式，是目前Web應用程式之間的準標準資料交換格式，在Rails之中，每個物件都有to_json方法可以很方便的轉換資料格式。
``` erb
<%= link_to 'ajax show', event_path(event), :remote => true, :data => { :type => :json }, :class => "ajax_update" %>
```
點擊ajax show就會送出Ajax request了，但接下來要怎麼撰寫處理JSON的程式呢？以下是一個範例：
``` js
<script>
$(document).ready(function() {
    $('.ajax_update').on("ajax:success", function(event, data) {
        var event_area = $('#event_area');
        event_area.html( data.name );
    });
});
</script>
```
使用 JSON 通常還會搭配 client-side template 機制，將回傳的資料搭配 template 後，再插入到 HTML 之中。

另一種變形的用法則是將HTML片段當做JSON的資料來傳遞。

另一種JSON的變形是JSONP(JSON with Padding)，將JSON資料包在一個JavaScript function裡，這個做的用處是讓這個API可以跨網域被呼叫。要回傳JSONP格式，需要用render :js並多一個參數是:callback。例如以下的程式可以搭配 jQuery 的 jsonp 格式：
``` erb
respond_to do |format|
  format.js { render :json => @user.to_json, :callback => params[:callback] }
end
```

#### Example code
https://github.com/ihower/rails-exercise-ac5/blob/master/app/views/welcome/ajax.html.erb (ac5)
https://github.com/ihower/rails-exercise-ac6/blob/master/app/views/welcome/ajax.html.erb (ac6)
https://github.com/ihower/rails-exercise-ac7/blob/master/app/views/welcome/ajax.html.erb (ac7)
https://github.com/ihower/rails-exercise-ac8/blob/master/app/views/welcome/ajax.html.erb (ac8)

#### Turbolinks
事實上，Rails預設讓每個換頁都用上了Ajax技巧，這一招叫做Turbolinks，在預設的Gemfile中可以看到gem "turbolinks"，以及Layout中的data-turbolinks-track。

它的作用是讓每一個超連結都只用Ajax的方式將整個body內容替換掉，這樣換頁時就不需要重新載入head部份的標籤，包括JavaScript和CSS等等，目的是可以改善換頁時的速度。

也因為它沒有整頁重新載入，所以如果有放在application.js裡面的 jQuery ready 事件處理，會變成只有第一次載入頁面才執行到，換頁時就失效了，所以必須改成 Turbolinks 的 page:change 事件，也就是：
``` js
$(document).ready(function(){
  //...
}
```
都要改寫成
``` js
$(document).on("page:change", function(){ 
 //...
})
```
如果想要明顯體會它的效果，可以在app/assets/javascripts/application.js裡面加上
``` js
Turbolinks.enableProgressBar();
```
最後，因為它會影響JavaScript的Event Bindings行為，所以在搭配一些JavaScript比較吃重的應用程式，例如使用JavaScript Framework如Backbone、AngularJS、Ember時會移除不用，以免互相影響。

或是你也可以針對特定的超連結，加上 data-no-turbolink 的屬性來關閉 Turbolinks：
``` html
<div id="some-div" data-no-turbolink>
  <a href="/">Home (without Turbolinks)</a>
</div>
```
例如 Facebook Share 的 Javascript code 就被 turbolinks 影響，請參考 turbolinks + facebook share button

