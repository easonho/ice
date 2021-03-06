---
title: 迁移到 react-router v4
order: 3
category: 其它
---

## 前言

react-router v4 相对于 react-router v3 几乎是重写了整个库, 新版的 react-router 遵循一切皆 React Component 的理念，路由即是组件，使路由更具声明式。

在 Iceworks 2.0.0 版本里支持了 react-router v4，所有的模板也做了相应的升级，使用 Iceworks 2.0.0 版本新创建的项目默认使用 react-router v4。 如果你是在这之前创建的项目，可以参考下面的 `Iceworks 路由管理方案`和`迁移步骤`进行对应的升级。

## Iceworks 路由管理方案

在 Iceworks 2.0.0 版本中统一对项目的路由配置信息进行增删改处理，不同的项目类型，根据约定的标准数据协议进行操作。主要包含以下三部分：

* menuConfig.js: 描述导航的结构关系
* routerConfig.js: 描述路由的结构关系
* router.js: 根据 routerConfig 数据协议实现对应的路由信息(react-router、vue-router)

**menuConfig.js**

```jsx
// 导航配置
// headerMenuConfig：头部导航配置
// asideMenuConfig：侧边导航配置
// 变量名 asideMenuConfig 为 iceworks 检测关键字，请不要修改名称

const headerMenuConfig = [
  {
    name: '首页',
    path: '/',
    icon: 'home',
  }
];

const asideMenuConfig = [
  {
    name: 'Dashboard',  // 名称
    path: '/',          // 路径
    icon: 'home',       // 图标
  },
  {
    name: '文章管理',
    path: '/post',
    icon: 'copy',
    children: [
      { name: '文章列表', path: '/post/list' },
      { name: '添加文章', path: '/post/create' },
    ],
  }
];

export { headerMenuConfig, asideMenuConfig };
```

**routerConfig.js**

```jsx
// 路由结构配置
// 每个页面支持配置不同的 Layout 结构
// 变量名 routerConfig 为 iceworks 检测关键字，请不要修改名称

const routerConfig = [
  {
    path: '/',                         // 页面路径
    layout: HeaderAsideFooterLayout,   // 页面渲染的布局
    component: Dashboard,              // 页面渲染的组件
  },
  {
    path: '/post',
    layout: HeaderAsideFooterLayout,
    component: PostList,
    children: [
      {
        path: 'list',
        layout: HeaderAsideFooterLayout,
        component: PostList,
      },
      {
        path: 'create',
        layout: HeaderAsideFooterLayout,
        component: CreatePost,
      },
    ],
  },
]
```

**router.js**
```jsx
// 主要根据 routerConfig 数据协议实现对应的路由信息，比如 react-router 转换后的格式如下：
// 完整实现参考：https://github.com/alibaba/ice/blob/master/react-materials/scaffolds/ice-design-cms/src/router.jsx

<Switch>
  <Route exact path="/" component={Home} />
  <Route exact path="/page3" component={Page3} />
  <Route exact path="/page4" component={Page4} />
  <Route exact path="/page3/:id" component={Page3} />
  <Route exact component={NotFound} />
</Switch>
```


## 迁移步骤

### 替换依赖包

在 v4 版本中，需要安装 `react-router` 和 `react-router-dom` 两个依赖包。

* react-router： React Router 核心组件
* react-router-dom：用于 DOM 绑定的 React Router

```
// package.json
"react-router": "^4.2.0",
"react-router-dom": "^4.2.2",
```

### 新建 `routerConfig.js` 文件

在 `src` 目录下新建 `routerConfig.js` 文件，将 `routes.js` 文件的代码按照约定的标准数据协议写入 `routerConfig.js` 后删除 `routes.js`。

**之前：routes.js**

```jsx
/**
 * 定义应用路由
 */
import React from 'react';
import { Router,hashHistory } from 'react-router';

// 路由配置规则参考： https://github.com/ReactTraining/react-router/blob/v3/docs/guides/RouteConfiguration.md#configuration-with-plain-routes
// 一下部分是由 ICE 相关工具自动生成的路由，请勿随意改变，否则可能会出现一些异常情况
// <!-- auto generated routes start -->
import HeaderAsideFooterLayout from './layouts/HeaderAsideFooterLayout';
import Dashboard from './pages/Dashboard';

const autoGeneratedRoutes = [
  {
    path: '/',
    component: HeaderAsideFooterLayout,
    indexRoute: { component: Dashboard },
  },
];

// <!-- auto generated routes end -->

// 自定义路由，如果 path 相同则会覆盖自动生成部分的路由配置
const customRoutes = [];

export default (
  <Router
    history={hashHistory}
    routes={[...autoGeneratedRoutes, ...customRoutes]}
  />
);
```

**现在：routerConfig.js**

```jsx
// 以下文件格式为描述路由的协议格式
// 你可以调整 routerConfig 里的内容
// 变量名 routerConfig 为 iceworks 检测关键字，请不要修改名称

import HeaderAsideFooterLayout from './layouts/HeaderAsideFooterLayout';
import Dashboard from './pages/Dashboard';

const routerConfig = [
  {
    path: '/',
    layout: HeaderAsideFooterLayout,
    component: Dashboard,
  }
];

export default routerConfig;

```

## 新建 `menuConfig.js` 文件

在 `src` 目录下新建 `menuConfig.js` 文件，将 `nav.js` 文件的代码按照约定的标准数据协议写入 `menuConfig.js` 后删除 `navs.js`。


**之前：navs.js**
```
// <!-- auto generated navs start -->
const autoGenHeaderNavs = [];
const autoGenAsideNavs = [];
// <!-- auto generated navs end -->

const customHeaderNavs = [
  {
    text: '首页',
    to: '/',
    icon: 'home',
  }
];

const customAsideNavs = [
  {
    text: 'Dashboard',
    to: '/',
    icon: 'home',
  }
];

function transform(navs) {
  // custom logical
  return [...navs];
}

export const headerNavs = transform([
  ...autoGenHeaderNavs,
  ...customHeaderNavs,
]);

export const asideNavs = transform([...autoGenAsideNavs, ...customAsideNavs]);
```

**现在：menuConfig.js**

```jsx
// 菜单配置
// headerMenuConfig：头部导航配置
// asideMenuConfig：侧边导航配置

const headerMenuConfig = [
  {
    name: '首页',
    path: '/',
    icon: 'home',
  }
];

const asideMenuConfig = [
  {
    name: 'Dashboard',
    path: '/',
    icon: 'home',
  }
];

export { headerMenuConfig, asideMenuConfig };
```


## 新建 `router.jsx` 文件

根据 `routerConfig.js` 数据协议实现对应的路由信息，目前飞冰支持 `react-router` 和 `vue-router` 的转换，具体实现参考：

* [根据 routerConfig.js 转换到 react-router v4 的路由实现](https://github.com/ReactTraining/react-router/blob/v3/docs/guides/RouteConfiguration.md#configuration-with-plain-routes)
* [根据 routerConfig.js 转换到 vue-router 的路由实现](https://github.com/alibaba/ice/blob/master/vue-materials/scaffolds/admin-lite/src/router.js)

## 其他

* 将使用 `react-router` 改成使用 `react-router-dom`

```jsx
  import { Link } from 'react-router';

  =>

  import { Link } from 'react-router-dom';
```

* 将使用 `this.props.location` 改成使用 `this.props.match`
```jsx
  const { location } = this.props;
  
  =>

  @withRouter
  class Page extends Component {
    ...
    const { match } = this.props;
    ...
  } 
```

* 导航菜单字段修改
  * 将 `text` 改成 `name`
  * 将 `to` 改成 `path`

* 其他请参考 react-router v4 [迁移文档](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/migrating.md)

至此，如果你按照上诉步骤进行迁移，即可完成 react-router v3 迁移到 react-router v4，如果在迁移过程中遇到问题，可以通过飞冰答疑群联系我们。

