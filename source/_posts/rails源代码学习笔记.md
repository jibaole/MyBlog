---
title: rails源代码学习笔记
tags:
  - rails
categories:
  - Web development
keywords:
  - rails
  - 源代码
description:
  - rails
  - 源代码
toc: true
date: 2016-09-09 15:02:33
feature:
---


#### Active Record

https://github.com/rails/rails/blob/master/activerecord/README.rdoc

* Automated mapping between classes and tables, attributes and columns.
* Associations between objects defined by simple class methods.
class Firm(公司) < ActiveRecord::Base
has_many :clients(一对多：有很多客户)
has_one :account(一对一：只有一个账目)
belongs_to :conglomerate(多对一：属于一个企业集团)
end

* Aggregations of value objects.
```
class Account < ActiveRecord::Base
  composed_of :balance, class_name: 'Money',
              mapping: %w(balance amount)(key value)
  composed_of :address,
              mapping: [%w(address_street street), %w(address_city city)]
end
```
<!-- more -->
* Validation rules that can differ for new or existing objects.
validates :terms_of_service, acceptance: true, on: :create(on: :create表示在在执行create方式时验证生效)

* Callbacks available for the entire life cycle (instantiation, saving, destroying, validating, etc.).
```
class Person < ActiveRecord::Base
  before_destroy :invalidate_payment_plan
  # the `invalidate_payment_plan` method gets called just before Person#destroy
  end
```

* Transactions.(事务)
```
# Database transaction
Account.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end
```

* Reflections on columns, associations, and aggregations.()
reflection = Firm.reflect_on_association(:clients)
reflection.klass # => Client (class)
Firm.columns # Returns an array of column descriptors for the firms table

* Database abstraction through simple adapters.
```
# connect to SQLite3
ActiveRecord::Base.establish_connection(adapter: 'sqlite3', database: 'dbfile.sqlite3')
# connect to MySQL with authentication
ActiveRecord::Base.establish_connection(
  adapter:  'mysql2',
  host:     'localhost',
  username: 'me',
  password: 'secret',
  database: 'activerecord'
)
```

* Logging support for Log4r and Logger.
ActiveRecord::Base.logger = ActiveSupport::Logger.new(STDOUT)
ActiveRecord::Base.logger = Log4r::Logger.new(‘Application Log’)

#### Active model
https://github.com/rails/rails/blob/master/activemodel/README.rdoc

* validates_presence_of :name 验证指定的属性不为空
* Add attribute magic to objects(?)
* Callbacks for certain operations(?)
* Tracking value changes(?)
* Adding errors interface to objects(?)
* Model name introspection(?)

#### Action Pack

1. Action Pack is a framework for handling and responding to web requests. It provides mechanisms for routing (mapping request URLs to actions), defining controllers that implement actions, and generating responses by rendering views, which are templates of various formats. In short, Action Pack provides the view and controller layers in the MVC paradigm.

2. It consists of several modules:
* Action Dispatch, which parses information about the web request, handles routing as defined by the user, and does advanced processing related to HTTP such as MIME-type negotiation, decoding parameters in POST, PATCH, or PUT bodies, handling HTTP caching logic, cookies and sessions.

* Action Controller, which provides a base controller class that can be subclassed to implement filters and actions to handle requests. The result of an action is typically content generated from views.


#### Action View
* Action View is a framework for handling view template lookup and rendering, and provides view helpers that assist when building HTML forms, Atom feeds and more. Template formats that Action View handles are ERB (embedded Ruby, typically used to inline short Ruby snippets inside HTML), and XML Builder.

#### Action Mailer(发送和接收邮件)

https://github.com/rails/rails/blob/master/actionmailer/README.rdoc

#### Active Job(?)

https://github.com/rails/rails/blob/master/activejob/README.md

#### Action Cable

https://github.com/rails/rails/blob/master/actioncable/README.md

* Action Cable seamlessly integrates WebSockets with the rest of your Rails application. It allows for real-time features to be written in Ruby in the same style and form as the rest of your Rails application, while still being performant and scalable. It’s a full-stack offering that provides both a client-side JavaScript framework and a server-side Ruby framework. You have access to your full domain model written with Active Record or your ORM of choice.



