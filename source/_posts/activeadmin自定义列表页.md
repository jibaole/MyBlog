---
title: activeadmin自定义列表页
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-07-18 10:54:44
keywords:
  - rails
  - activeadmin
  - 自定义列表
description:
  - rails
  - activeadmin
  - 自定义列表
feature:
---

### 列表页

#### 最简单的列表样式
``` ruby
  index do
    column :title
    default_actions
  end
```

index页面有多种样式，可选的有：

* Table: A table drawn with each row being a resource
* Grid: A set of rows and columns each cell being a resource
* Blocks: A set of rows (not tabular) each row being a resource
* Blog: A title and body content, similar to a blog index
<!-- more -->

#### 显示外键 
如果表格里有外键，如Post 中有 Category id。
直接写 column :category_id 必然会显示一个数字，而我们要的肯定是显示 Category Name。

因此需要在 Category Model中，定义一个category_name方法。
``` ruby
def category_name
    self.name
end
```
使用时
``` ruby
 index do
    column :title
    column :category_name
    default_actions
  end
```

#### 设置是否分页
``` ruby
ActiveAdmin.register Post do
  config.paginate = false
end
```

#### 设置每页条数
``` ruby
ActiveAdmin.register Post do
  config.per_page = 10
end
```

#### 设置默认排序条件
``` ruby
ActiveAdmin.register Post do
  config.sort_order = 'name_asc'
end
```

#### 禁用删除功能
``` ruby
ActiveAdmin.register Post do
  actions :all, except: [:destroy]
end
```

#### 禁用新建功能
``` ruby
ActiveAdmin.register Post do
  actions :all, except: [:new]
end
```

#### 禁用更新功能
``` ruby
ActiveAdmin.register Post do
  actions :all, except: [:update]
end
```

### 过滤器

#### 最简单的过滤器
``` ruby
filter :title
```

#### 指定标签
``` ruby
filter :title, label: '标题'
```

#### 指定类型
``` ruby
filter :title, as: :string, label: "标题"
```
  过滤器类型: 
  :string 输入框 
  :date_range 选择日期区间 
  :numeric 选择“等于”、“包含”、“” 
  :select 下拉框 
  :check_boxes 勾选框

#### 下拉选择筛选
``` ruby
  filter :organization_id, :as=>:select, collection: proc { Organization.all }, :label=>"机构"
```

### 关闭过滤器

#### 1、局部关闭 
仅在Post中关闭过滤器。
``` ruby
ActiveAdmin.register Post do
  config.filters = false
end
```
#### 2、全局关闭 
关闭后，所有模块都不会显示过滤器。
``` ruby
ActiveAdmin.setup do |config|
  config.filters = false
end
```

转自：http://blog.csdn.net/feng88724/article/details/49124281