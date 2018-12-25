## 三类导航钩子：
[详解vue-router 中的导航钩子](https://blog.csdn.net/weixin_41399785/article/details/79382243)

作用：主要用来作用是拦截导航，让他完成跳转或取消。

### 1 、全局导航钩子:

beforeEach、前置守卫、
beforeResolve、
afterEach、后置钩子

### 2 、某个路由独享的导航钩子:

beforeEnter


### 3 、路由组件上的导航钩子

1.beforeRouteEnter、

2.beforeRouteUpdate (2.2 新增)、

3.beforeRouteLeave

```
// 拦截浏览器后退实现草稿保存类似需求
//在路由组件中
beforeRouteLeave (to, from, next) {
    if(用户已经输入信息){
    //出现弹窗提醒保存草稿，或者自动后台为其保存
    }else{
    next(true);//用户离开
    }
}

```

### 完整的导航解析流程：

导航被触发
- 在失活的组件里调用离开守卫
- 调用全局的 beforeEach 守卫
- 在重用的组件里调用 beforeRouteUpdate 守卫
- 在路由配置里调用 beforEnter
- 解析异步路由组件
- 在被激活的组件里调用 beforeRouteEnter
- 调用全局的 beforeResolve 守卫
- 导航被确认
- 调用全局的 afterEach 钩子
- 触发 DOM 更新
- 在创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数


