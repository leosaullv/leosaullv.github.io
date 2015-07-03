---
layout: post
title:  "ruby代理模式"
date:   2015-06-09 14:30:00
categories: ruby
tags: refactoring
author: "leosaullv"
---

###Ruby 代理模式


在面向对象的程序设计里，有许多大的逻辑可能会有许多小的逻辑去实现。例如，User会负责用户的特定信息，如电话号码，电子邮件，用户验证等，User是一个模型，他有许多的属性以及功能。它被如何用到应用系统的很多方面呢？我们通常用代理模式来实现。

#####在ruby里实现代理模式通常有以下集中方式

- 继承代理
```ruby
class User
  def phone
    #return the phone number of user
  end
  def email
    #return the email of user
  end 
  def auth?
    #right user?
  end
end
```
```
class InputUserEmail < User
  def auth?
    super
  end
end
```
在这里，我们把验证逻辑都封装到了User模型中，避免了别的地方调用所带来的重复操作。更有利于逻辑的更改。
    - 优点：把业务逻辑封装起来，当需要改动的时候，只改动业务逻辑即可
    - 缺点：要调用这块逻辑，必须继承才可，对于ruby的单继承而言，很显然是不利的。
  - 参数代理
```ruby
class User
  def email
    #return the email of user
  end
  def info(delegate)
    delegate.generate(self.email)
  end
end

class CsvFormatter
  def generate(records)
    #format to csv
  end
end

class JsonFormatter
  def generate(records)
    #format to json
  end
end

class XmlFormatter
  def generate(records)
    #format to xml
  end
end
user = User.new
user.info(CsvFormatter.new)
user.info(JsonFormatter.new)
user.info(XmlFormatter.new)
```
代理对象被传递给对象。对象将数据转化为所需要的格式。对象只关心需要改变格式的数据，至于改变成什么样的格式都是传递过来的代理对象所决定的。
 -优点：让对象拥有主动权，可以去选择代理
 -缺点:  只限于相同的接口，所有代理都必须实现这个接口

- method_missing
```
class User
  attr_reader :product
  def initialize(product)
    @product = product
  end
  def method_missing(method,*args)
    if method.match(/product_(.+)/
      self.product.send($1,*args)
    else
      super
    end
  end
end

class Product
  attr_accessor :name, :description, :price
end

User.new(Product.new).product_price
```
我们可以使用Ruby的method_missing做到这一点，捕获没有明确定义的消息。
-优点：易于扩展
-缺点：参数传递较多，不宜控制

- Forwardable
```
require 'forwardable'
class User
  extend Forwardable
  def_delegators :@product,:name,:descriptions,:price
  attr_reader :product
  def initialize(product)
    @product = product
  end
end

class Product
  attr_accessor :name, :description, :price
end
product = Product.new
product.price = 10
User.new(product).price
```
`def_delegators` 指明了把 User要调用的方法（name, description,price）绑定到`@product`上，调用`@user.price` 就相当于调用了`@product.price`.
关于	`forwardable`的用法可以参考[这里](http://brainspec.com/blog/2012/11/07/delegation-with-forwardable/)

参考文章：
[Delegation patterns in Ruby](http://radar.oreilly.com/2014/02/delegation-patterns-in-ruby.html)
[Delegation on a method by method basis with Forwardable](http://brainspec.com/blog/2012/11/07/delegation-with-forwardable/)
[Forwardable](http://ruby-doc.org/stdlib-2.0.0/libdoc/forwardable/rdoc/Forwardable.html)