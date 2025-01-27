---
template: blog.html
author: ginuerzh
author_gh_user: ginuerzh
read_time: 15min
publish_date: 2022-09-09 22:00
---

[选择器](/concepts/selector/)，顾名思义是用来做选择的，通过在一组对象中进行筛选，最终选出一个我们想要的结果，这就是选择器的工作。

当[转发链](/concepts/chain/)的一个层级(或跳跃点)中使用多个节点时，由于每一次请求只能使用其中的一个，这时就需要我们做选择了。

!!! tip "内部实现"
 
  一个选择器是由一个选择策略(Strategy)加上若干个过滤器(Filter)组成，当执行选择时，会先依次执行过滤器进行过滤，最后再使用选择策略选出最终的对象。当经过过滤器过滤后无可用对象时，选择器会返回空。

目前的选择器支持两种类型对象：节点和转发链。