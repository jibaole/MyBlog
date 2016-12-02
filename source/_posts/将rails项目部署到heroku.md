---
title: 将rails项目部署到heroku
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-07-18 11:36:48
description: 转自：https://ihower.tw/rails/heroku.html
feature:
---

Heroku提供了 PaaS 雲端服務，讓 Ruby 應用程式可以跑在上頭(所以不只是 Rails，像是 Sinatra 也可以跑在上面)，以及提供 PostgreSQL 這套關聯式資料庫和其他許多的外掛服務。它的底層硬體資源是採用了可擴展的 Amazon EC2，透過 Heroku 的 dynos (它的資源單位) 只要拉上拉下就可以增加伺服器負載了，非常厲害，請看 Rapportive 的故事：

>想要擁有一個可隨時延展的架構(scalable architecture)，你只要選對廠商(hosting provider)就可以做到。如果Rapportive一開始隨便選了一個便宜的VPS，流量一衝進來的時候可能就會像全面啟動電影中Cobb的混沌世界(Limbo)一樣崩潰。Rapportive的服務是放在知名廠商Heroku上，對於突然湧進的流量只需要增加Dynos的數量（Heroku提供服務的基本單位），基本上你是不需要修改你的程式的；當然，程式的優化、調整可以在同樣能耐的硬體等級上容納更多人。
<!-- more -->

使用Heroku、不需要調整程式、只需要增加Dyno數量？真的有這麼美好嗎？事實上Rapportive就是這麼辦到的，在來自全世界的流量突然湧進時，Rahul Vohra手邊沒有電腦，於是他隨即拿起iPhone並且利用Nezumi這個設計來管理Heroku的應用程式，將Rapportive的Dynos增加到20個，就這麼簡單，可能不到一分鐘吧？！系統的能耐馬上就提昇了。

不過，這麼方便的工具也是有一些缺點。首先它的價格昂貴，當你的網站有穩定流量時會不划算，另外他的伺服器位在美國或歐洲，從亞洲連過去比較慢。最後是有Vendor lock-in的風險。因此，通常我們只利用它的提供的免費方案，作為玩具專案的展示之用，

#### 第一次設定 Heroku
1. 註冊[Heroku](https://heroku.com/) 帳號
2. 下載及安裝[toolbelt](https://toolbelt.heroku.com/) 工具
3. Heroku預設使用PostgreSQL資料庫，所以我們也在本機安裝PostgreSQL，請執行 `brew install postgresql`

####  第一次在 Heroku 開 App
前提：Heroku 使用Git進行佈署，你的專案必須用Git做版本控制。

請在你的專案目錄下執行 `heroku create`

#### 更新你的Code
1. Gemfile 加上 pg gem 和 rails_12factor gem，如果你本來用 sqlite3 或 mysql2 gem，請移除或搬到只有 :development 和 :test 模式才使用 。
```
 gem 'pg'
 gem 'rails_12factor', group: :production

 group :development, :test do
   gem 'sqlite3'
   # ....
 end
```
2. bundle
3. config/database.yml 的 production 改用 postgresql adapter:
```
 production:
   adapter: postgresql
   encoding: unicode
```
4. commit 你的修改

#### 部署到 Heroku
1. 在你的專案目錄下執行 git push heroku master 將程式推送到 heroku 去
2. 執行 heroku run rake db:migrate
3. 執行 heroku open 就會打開瀏覽器了

#### 如何瀏覽 Logs?
``` bash
heroku logs --tail
```
#### 如何打開遠端的 Rails console？
``` bash
heroku run rails console
```