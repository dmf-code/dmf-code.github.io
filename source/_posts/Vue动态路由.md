---
title: Vue动态路由
date: 2021-04-06 22:24:49
tags: 前端
categories: Vue
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210409224604.jpeg
---

### 为什么使用动态路由？

因为后台会存在角色，角色中会有不同的菜单数据，这里面就会出现路由不能写死的问题？如何解决，上动态路由。

### 设计思路

1. 登录的时候对异步请求数据保存到 `localstorage` 中
2. 在 `router.beforeEach` 路由守卫中添加 `localstorage` 菜单数据加载
3. 根据加载的菜单数据，递归加载组件，因为本地只保存 `component` 路径
4. 最后使用 `router.addRoutes` 将组装好的数据填充进来就完成了

### 坑点

#### 为什么路由不报错，进入该路由界面无显示任何数据？

这是因为思想出问题了，一开始在登录中将 `component` 直接 `import` 进来了，然后再保存到 `localstorage` 。所以理所当然认为本地保存了组件信息，这个和周总（同事）讨论过。他也不知道怎么回事，但是我突然就说没有加载组件进来，果然陷入思维困局需要第二者。

##### 解决方案：

`component` 字段保存组件路径，在需要加载的时候再 `import` 进来。

```javascript
function BuildRoutes(routes) {
  let element = {
    name: routes.name,
    path: routes.path,
    component: () => import(`@/${routes.component}.vue`),
  };

  if (routes.component == "") {
    delete element.component;
  }

  if (!isNull(routes.children) || !isUndefined(routes.children)) {
    element.children = [];
    routes.children.forEach((item) => {
      element.children.push(BuildRoutes(item));
    });

    return element;
  }
  return element;
}
```

#### 如何保证无路由时后台获取？

正如标题所讲，我首先的思路就是在 `router.beforeEach` 中判断无路由就直接后台请求获取。这样子做就会有一个问题，那就是如何判断后台数据获取到了的问题。因为在守卫中是同步的，而我在这里使用的是异步库 `axios` 。所以这里就会导致短期会有多个请求

##### 解决方案：

将路由信息在登录中保存，这样子就不会出现多次请求问题，因为登录只会进行一次，所以请求后台数据并保存到本地也只会是一次。

```javascript
// 递归组装路由信息
function dynamicBuild(source) {
  let element = {
    name: source.label,
    path: source.full_url,
    component: source.component,
  };

  if (source.children != null) {
    element.children = [];
    source.children.forEach((item) => {
      element.children.push(dynamicBuild(item));
    });

    return element;
  }

  return element;
}
```

```javascript
// vuex mutations 函数
generateRoutes: (state, routes) => {
  state.addRouters = routes;
  let routers = BuildRoutes(routes);
  if (isObject(routers)) {
    routers = [routers];
  }
  router.addRoutes(routers);
};
```

```javascript
// 动态路由请求数据获取
function dynamicRouter() {
  menuList().then((res) => {
    let backendRouter;
    backendRouter = dynamicBuild(res.data.data[0]);
    storage.setItem({
      name: "backend_router",
      value: backendRouter,
      expires: new Date().getTime() + 60 * 60 * 1000,
    });
    store.dispatch("GenerateRoutes", backendRouter);
  });
}
```

#### 如何保证动态路由信息不会重复添加？

首先在路由守卫当中，我们要保证添加过路由后就不再添加进来。所以我们就要在全局中保存所需要的信息，这里买了 `vuex` 就是非常好的方法。首先我们应该在 `state` 中添加一个 `addRouters` 变量，默认是 `null` 然后就可以根据这个值来判断是否需要添加了。
