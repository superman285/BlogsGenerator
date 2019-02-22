---
title: Vue的lifecycle
tags:
  - Vue
  - 生命周期
categories:
  - FrontEnd
  - Vue
date: 2019-01-06 19:32:12
---



##### Vue生命周期



从初始化到调用结束

![vuelifecycle](vuelifecycle.png)



实例创建完成后立即调用created 实例已经完成以下配置:数据观测(object observer) 属性和方法计算	



created阶段时 $el还不存在

mounted阶段时 $el就有了



vm.$mount(el) 手动指定挂载对象





template指定的模板优先于el的outerHTML

如果没有template选项，会将el.outerHTML作为template (outerHTML包括自身标签 而不是只是儿子)



注💡:

模板不同于视图



模板+数据=>编译后得到视图(用户看到的)



vue实例的生命周期,vue实例本质上也是组件，根组件

组件的生命周期:

动态组件切换时 例如有ma切换到mb，mb挂载前先销毁了ma再成功挂载好mb

beforeCreate->created->beforeMount->beforeDestroy->destroyed->mounted



==keep-alive==

不会将组件反复地重新创建和销毁，而是缓存起来，切换新组件时就不会destroy老组件，切换到老组件不会重新创建和挂载

有特殊需求时可以使用



生命周期activated和deactivated 针对keep-alive而言的

当切换组件时 activated新组件 deactivated老组件



