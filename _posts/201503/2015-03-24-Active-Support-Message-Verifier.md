---
layout: post
title:  "ActiveSupport MessageVerifier"
date:   2015-03-24 12:20:00
categories: rails
tags: tip
author: "Victor"
---

rails 的 `ActiveSupport` 组件提供了很多非常有用的小功能，如果善于利用的话，可以使我们在实际项目中减少很多重复造轮子的事情。今天我们介绍的主角就是：`MessageVerifier`。

`MessageVerifier` 提供了加密和解密消息的功能，可以有效的防止信息被伪造。其源码位于 [active_support/message_verifier.rb](http://api.rubyonrails.org/classes/ActiveSupport/MessageVerifier.html) 文件，下面介绍使用方法及原理：

## 应用场景

正常情况下，http协议对信息都是进行明文传输，用过抓包工具的同学一定印象很深刻，我们通过form表单提交的密码信息实际是明文传输非常透明的。 当然我们可以使用https对web信息进行加密后传输，不过https会牺牲一些性能和速度，于是一些敏感信息我们便可以单独加密传输。

举个例子，我们经常在cookie中存储 remember me 的相关信息，包括过期时间等内容。这个时候我们就可以对相关内容进行加密。

本周的开发工作中，我也遇到了一个需要加密传输的需求：当我们的用户连续签到达到7天，我们将赋予这个用户解锁某个功能的权限。我们会带着用户token参数去调用一个接口。 用户是很容易知道自己的token的，如果某个用户用自己的token去请求这个接口，不就为自己开通了权限。这个时候我们最好的办法就是对token进行加密，然后收到请求后再解密。

## 工作原理

加密的过程就是以某个流程将信息转化成另一个样子，解密的过程就是以相对的过程将“另一个样子”的信息还原成本来的信息。所以说这个类中一定提供了两个方法，一个是加密的，一个是解密的。 这两个对应的方法是：

* 加密：`generate`
* 解密：`verify`

这两个方法是 `ActiveSupport::MessageVerifier` 的对象方法。下面我们先来 `new` 一个对象。 `ActiveSupport::MessageVerifier` 对象会包含下面几个信息：

* `@secret` 俗称暗号，没有默认值，`new` 方法中必须提供；
* `@digest` 采用的摘要算法，默认 SHA1，可选的有：MD5 / SHA1 / SHA2等；
* `@serializer` 对象序列化方法，默认是：Marshal，可选的有YAML, JSON, Marshal等；

```ruby
# new 对象
@verifier = ActiveSupport::MessageVerifier.new('123456')
# or @verifier = ActiveSupport::MessageVerifier.new(Rails.application.secrets.password_reset_secret)
# => #<ActiveSupport::MessageVerifier:0x007fe522132ba8 @secret="123456", @digest="SHA1", @serializer=Marshal>

# new 对象并指定serializer 为 YAML
@verifier = ActiveSupport::MessageVerifier.new('123456', serializer: YAML)
# => #<ActiveSupport::MessageVerifier:0x007fe522103448 @secret="123456", @digest="SHA1", @serializer=Psych>
```

要加密的内容可为任何形式的对象，`MessageVerifier` 对象会以指定的对象序列化方法进行序列化。

示例中我们加密的对象是一个包含用户 id 及过期时间的数组：`[user_id, time]`

```ruby
# 加密
cookies[:remember_me] = @verifier.generate([@user.id, 2.weeks.from_now])

# 解密
id, time = @verifier.verify(cookies[:remember_me])
if time < Time.now # 应用：判断是否过期并查找用户
  @current_user = User.find(id)
end
```

## 延伸阅读

* [原文 ActiveSupport宝藏之MessageVerifier](http://www.xinshengyin.com/ruby/activesupport-message-verifier/)
* [Reading Rails - How Does MessageVerifier Work?](http://www.monkeyandcrow.com/blog/reading_rails_how_does_message_verifier_work/)
* [Signing and encrypting data with tools built-in to Rails](http://vesavanska.com/2013/signing-and-encrypting-data-with-tools-built-in-to-rails/)
* [Rails 4.1 Message Verifier](https://www.youtube.com/watch?v=qNJbe1HWXDA)
