<img width="112" alt="screen shot 2016-10-25 at 2 37 27 pm" src="https://cloud.githubusercontent.com/assets/13041/19686250/971bf7f8-9ac0-11e6-975c-188defd82df1.png">

[![NPM version](https://img.shields.io/npm/v/next.svg)](https://www.npmjs.com/package/next)
[![Build Status](https://travis-ci.org/zeit/next.js.svg?branch=master)](https://travis-ci.org/zeit/next.js)
[![Build status](https://ci.appveyor.com/api/projects/status/gqp5hs71l3ebtx1r/branch/master?svg=true)](https://ci.appveyor.com/project/arunoda/next-js/branch/master)
[![Coverage Status](https://coveralls.io/repos/zeit/next.js/badge.svg?branch=master)](https://coveralls.io/r/zeit/next.js?branch=master)
[![Join the community on Spectrum](https://withspectrum.github.io/badge/badge.svg)](https://spectrum.chat/next-js)

Next.js is a minimalistic framework for server-rendered React applications.
Next.js 是一个轻巧的React服务端渲染应用框架。

**可访问 [nextjs.org/learn](https://nextjs.org/learn) 开始学习 Next.js.**

---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
<!-- https://github.com/thlorenz/doctoc -->

- [安装](#how-to-use)
  - [Setup](#setup)
  - [Automatic code splitting](#automatic-code-splitting)
  - [CSS](#css)
    - [Built-in CSS support](#built-in-css-support)
    - [CSS-in-JS](#css-in-js)
    - [Importing CSS / Sass / Less / Stylus files](#importing-css--sass--less--stylus-files)
  - [Static file serving (e.g.: images)](#static-file-serving-eg-images)
  - [Populating `<head>`](#populating-head)
  - [Fetching data and component lifecycle](#fetching-data-and-component-lifecycle)
  - [Routing](#routing)
    - [With `<Link>`](#with-link)
      - [With URL object](#with-url-object)
      - [Replace instead of push url](#replace-instead-of-push-url)
      - [Using a component that supports `onClick`](#using-a-component-that-supports-onclick)
      - [Forcing the Link to expose `href` to its child](#forcing-the-link-to-expose-href-to-its-child)
      - [Disabling the scroll changes to top on page](#disabling-the-scroll-changes-to-top-on-page)
    - [Imperatively](#imperatively)
    - [Intercepting `popstate`](#intercepting-popstate)
      - [With URL object](#with-url-object-1)
      - [Router Events](#router-events)
      - [Shallow Routing](#shallow-routing)
    - [Using a Higher Order Component](#using-a-higher-order-component)
  - [Prefetching Pages](#prefetching-pages)
    - [With `<Link>`](#with-link-1)
    - [Imperatively](#imperatively-1)
  - [Custom server and routing](#custom-server-and-routing)
    - [Disabling file-system routing](#disabling-file-system-routing)
    - [Dynamic assetPrefix](#dynamic-assetprefix)
  - [Dynamic Import](#dynamic-import)
    - [1. Basic Usage (Also does SSR)](#1-basic-usage-also-does-ssr)
    - [2. With Custom Loading Component](#2-with-custom-loading-component)
    - [3. With No SSR](#3-with-no-ssr)
    - [4. With Multiple Modules At Once](#4-with-multiple-modules-at-once)
  - [Custom `<App>`](#custom-app)
  - [Custom `<Document>`](#custom-document)
  - [Custom error handling](#custom-error-handling)
  - [Reusing the built-in error page](#reusing-the-built-in-error-page)
  - [Custom configuration](#custom-configuration)
    - [Setting a custom build directory](#setting-a-custom-build-directory)
    - [Disabling etag generation](#disabling-etag-generation)
    - [Configuring the onDemandEntries](#configuring-the-ondemandentries)
    - [Configuring extensions looked for when resolving pages in `pages`](#configuring-extensions-looked-for-when-resolving-pages-in-pages)
    - [Configuring the build ID](#configuring-the-build-id)
  - [Customizing webpack config](#customizing-webpack-config)
  - [Customizing babel config](#customizing-babel-config)
  - [Exposing configuration to the server / client side](#exposing-configuration-to-the-server--client-side)
  - [Starting the server on alternative hostname](#starting-the-server-on-alternative-hostname)
  - [CDN support with Asset Prefix](#cdn-support-with-asset-prefix)
- [Production deployment](#production-deployment)
- [Static HTML export](#static-html-export)
  - [Usage](#usage)
  - [Limitation](#limitation)
- [Multi Zones](#multi-zones)
  - [How to define a zone](#how-to-define-a-zone)
  - [How to merge them](#how-to-merge-them)
- [Recipes](#recipes)
- [FAQ](#faq)
- [Contributing](#contributing)
- [Authors](#authors)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## How to use

### Setup

安装它:

```bash
npm install --save next react react-dom
```

将下面脚本天骄到 package.json 中:

```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

下面, 文件系统是主要的API. 每个`.js` 文件将变成一个路由，自动处理和渲染。

Every `.js` file becomes a route that gets automatically processed and rendered.

新建 `./pages/index.js` 到你的项目中:

```jsx
export default () => <div>Welcome to next.js!</div>
```

运行 `npm run dev` 命令并打开 `http://localhost:3000`。 如果你想使用其他端口，可运行 `npm run dev -- -p <设置端口号>`.


So far, we get:
目前为止我们可以了解到:

- 自动打包编译 (使用 webpack 和 babel)
- 热加载
- 以 `./pages`作为服务端的渲染和索引
- Static file serving. `./static/` is mapped to `/static/` (given you [create a `./static/` directory](#static-file-serving-eg-images) inside your project)
- 静态文件服务. `./static/` 映射到 `/static/` (可以 [创建一个静态目录](#static-file-serving-eg-images) 在你的项目中)

这里有个简单的案例，可以下载看看 [sample app - nextgram](https://github.com/zeit/nextgram)

### Automatic code splitting

代码自动分割

每个页面只会导入`import`中绑定以及被用到的代码. 也就是说并不会加载不需要的代码!

```jsx
import cowsay from 'cowsay-browser'

export default () =>
  <pre>
    {cowsay.say({ text: 'hi there!' })}
  </pre>
```

### CSS

#### Built-in CSS support

支持嵌入样式

<p><details>
  <summary><b>案例</b></summary>
  <ul><li><a href="https://github.com/zeit/next.js/tree/canary/examples/basic-css">Basic css</a></li></ul>
</details></p>

我们绑定 [styled-jsx](https://github.com/zeit/styled-jsx) 来生成独立作用域的CSS. 目标是支持 "shadow CSS",但是 [不支持独立模块作用域的js](https://github.com/w3c/webcomponents/issues/71).

```jsx
export default () =>
  <div>
    Hello world
    <p>scoped!</p>
    <style jsx>{`
      p {
        color: blue;
      }
      div {
        background: red;
      }
      @media (max-width: 600px) {
        div {
          background: blue;
        }
      }
    `}</style>
    <style global jsx>{`
      body {
        background: black;
      }
    `}</style>
  </div>
```

想查看更多案例可以点击 [styled-jsx documentation](https://www.npmjs.com/package/styled-jsx)查看.

#### CSS-in-JS

内嵌样式

<p><details>
  <summary>
    <b>Examples</b>
    </summary>
  <ul><li><a href="./examples/with-styled-components">Styled components</a></li><li><a href="./examples/with-styletron">Styletron</a></li><li><a href="./examples/with-glamor">Glamor</a></li><li><a href="./examples/with-glamorous">Glamorous</a></li><li><a href="./examples/with-cxs">Cxs</a></li><li><a href="./examples/with-aphrodite">Aphrodite</a></li><li><a href="./examples/with-fela">Fela</a></li></ul>
</details></p>

有些情况可以使用css内嵌js写法。如下所示：

```jsx
export default () => <p style={{ color: 'red' }}>hi there</p>
```

更复杂的内嵌样式解决方案，特别是服务端渲染的时样式更改。我们可以通过包裹自定义Document，来添加样式，案例如下：[custom `<Document>`](#user-content-custom-document)

#### Importing CSS / Sass / Less / Stylus files
使用 CSS / Sass / Less / Stylus files

To support importing `.css`, `.scss`, `.less` or `.styl` files you can use these modules, which configure sensible defaults for server rendered applications.
支持用`.css`, `.scss`, `.less` or `.styl`，需要配置默认文件next.config.js，具体可查看下面链接

- [@zeit/next-css](https://github.com/zeit/next-plugins/tree/master/packages/next-css)
- [@zeit/next-sass](https://github.com/zeit/next-plugins/tree/master/packages/next-sass)
- [@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)
- [@zeit/next-stylus](https://github.com/zeit/next-plugins/tree/master/packages/next-stylus)

### Static file serving (e.g.: images)
静态文件服务（如图像）

Create a folder called `static` in your project root directory. From your code you can then reference those files with `/static/` URLs:
在根目录下新建文件夹叫`static`。代码可以通过`/static/`来引入相关的静态资源。

```jsx
export default () => <img src="/static/my-image.png" alt="my image" />
```

_注意：不要自定义静态文件夹的名字，只能叫`static` ，因为只有这个名字Next.js才会把它当作静态资源。

### Populating `<head>`

`<head>`

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/head-elements">Head elements</a></li>
    <li><a href="./examples/layout-component">Layout component</a></li>
  </ul>
</details></p>

我们设置一个内置组件来装载`<head>`到页面中。

```jsx
import Head from 'next/head'

export default () =>
  <div>
    <Head>
      <title>My page title</title>
      <meta name="viewport" content="initial-scale=1.0, width=device-width" />
    </Head>
    <p>Hello world!</p>
  </div>
```

To avoid duplicate tags in your `<head>` you can use the `key` property, which will make sure the tag is only rendered once:
我们定义`key`属性来避免重复的`<head>` 标签，保证`<head>` 只渲染一次，如下所示：

```jsx
import Head from 'next/head'
export default () => (
  <div>
    <Head>
      <title>My page title</title>
      <meta name="viewport" content="initial-scale=1.0, width=device-width" key="viewport" />
    </Head>
    <Head>
      <meta name="viewport" content="initial-scale=1.2, width=device-width" key="viewport" />
    </Head>
    <p>Hello world!</p>
  </div>
)
```

In this case only the second `<meta name="viewport" />` is rendered.
只有第二个`<meta name="viewport" />`才被渲染。

_Note: The contents of `<head>` get cleared upon unmounting the component, so make sure each page completely defines what it needs in `<head>`, without making assumptions about what other pages added_
注意：在卸载组件时，' <head> '的内容将被清除。请确保每个页面都在其`<head>`定义了所需要的内容，而不是假设其他页面已经加过了

### Fetching data and component lifecycle
获取数据以及组件生命周期

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/data-fetch">Data fetch</a></li></ul>
</details></p>

如果你需要一个有状态、生命周期或有初始数据的React组件（而不是上面的无状态函数），如下所示：

```jsx
import React from 'react'

export default class extends React.Component {
  static async getInitialProps({ req }) {
    const userAgent = req ? req.headers['user-agent'] : navigator.userAgent
    return { userAgent }
  }

  render() {
    return (
      <div>
        Hello World {this.props.userAgent}
      </div>
    )
  }
}
```

相信你注意到，当页面渲染时加载数据，我们使用了一个异步方法`getInitialProps`。它能异步获取js普通对象，并绑定在`props`上

Data returned from `getInitialProps` is serialized when server rendering, similar to a `JSON.stringify`. Make sure the returned object from `getInitialProps` is a plain `Object` and not using `Date`, `Map` or `Set`.
当服务渲染时，`getInitialProps`将会把数据序列化，就像`JSON.stringify`。所以确保`getInitialProps`返回的是一个普通js对象，而不是`Date`, `Map` 或 `Set`类型。

For the initial page load, `getInitialProps` will execute on the server only. `getInitialProps` will only be executed on the client when navigating to a different route via the `Link` component or using the routing APIs.

当页面初始化加载时，`getInitialProps`只会加载在服务端。只有当路由跳转（`Link`组件跳转或API方法跳转）时，客户端才会执行`getInitialProps`。

_Note: `getInitialProps` can **not** be used in children components. Only in `pages`._

注意：`getInitialProps`将不能使用在子组件中。只能使用在`pages`页面中。

<br/>

> 只有服务端用到的模块放在 `getInitialProps`里，请确保正确的导入了它们，可参考[import them properly](https://arunoda.me/blog/ssr-and-server-only-modules)。
> 否则会拖慢你的应用速度。

<br/>

You can also define the `getInitialProps` lifecycle method for stateless components:
你也可以给5⃣无状态组件定义 `getInitialProps`：

```jsx
const Page = ({ stars }) =>
  <div>
    Next stars: {stars}
  </div>

Page.getInitialProps = async ({ req }) => {
  const res = await fetch('https://api.github.com/repos/zeit/next.js')
  const json = await res.json()
  return { stars: json.stargazers_count }
}

export default Page
```

`getInitialProps` receives a context object with the following properties:
`getInitialProps`入参对象的属性如下：

- `pathname` - path section of URL
- `pathname` - URL的path部分
- `query` - query string section of URL parsed as an object
- `query` - URL的query部分，并被解析成对象
- `asPath` - `String` of the actual path (including the query) shows in the browser
- `asPath` - 显示在浏览器中的实际路径（包含查询部分），为`String`类型
- `req` - HTTP request object (server only)
- `req` - HTTP请求对象 (只有服务器端有)
- `res` - HTTP response object (server only)
- `res` - HTTP返回对象 (只有服务器端有)
- `jsonPageRes` - [Fetch Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) object (client only)
- `jsonPageRes` - [获取数据响应对象](https://developer.mozilla.org/en-US/docs/Web/API/Response) (只有客户端有)
- `err` - Error object if any error is encountered during the rendering
- `err` - 渲染过程中的任何错误

### Routing
### 路由

#### With `<Link>`
#### `<Link>`用法

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/hello-world">Hello World</a></li>
  </ul>
</details></p>

Client-side transitions between routes can be enabled via a `<Link>` component. Consider these two pages:
可以用 `<Link>` 组件实现客户端的路由切换。

```jsx
// pages/index.js
import Link from 'next/link'

export default () =>
  <div>
    Click{' '}
    <Link href="/about">
      <a>here</a>
    </Link>{' '}
    to read more
  </div>
```

```jsx
// pages/about.js
export default () => <p>Welcome to About!</p>
```

__Note: use [`<Link prefetch>`](#prefetching-pages) for maximum performance, to link and prefetch in the background at the same time__
注意：可以使用[`<Link prefetch>`](#prefetching-pages)使链接和预加载在后台同时进行，来达到页面的最佳性能。

Client-side routing behaves exactly like the browser:
客户端路由行为与浏览器很相似：

1. The component is fetched
1. 组件获取
2. If it defines `getInitialProps`, data is fetched. If an error occurs, `_error.js` is rendered2. 
2. 如果组件定义了`getInitialProps`，数据获取了。如果有错误情况将会渲染 `_error.js`。
3. After 1 and 2 complete, `pushState` is performed and the new component is rendered
3. 1和2都完成了，`pushState`执行，新组件被渲染。

**Deprecated, use [withRouter](https://github.com/zeit/next.js#using-a-higher-order-component) instead** - Each top-level component receives a `url` property with the following API:
**不建议使用该特性，使用[withRouter](https://github.com/zeit/next.js#using-a-higher-order-component)来代替** - 每个顶级组件都接收`url` 属性，API如下：

- `pathname` - `String` of the current path excluding the query string
- `pathname` - 不包含查询内容的当前路径，为`String`类型
- `query` - 查询内容，被解析成`Object`类型. 默认为`{}`
- `asPath` - 展现在浏览器上的实际路径，包含查询内容，为`String`类型

##### With URL object
##### URL 对象

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/with-url-object-routing">With URL Object Routing</a></li>
  </ul>
</details></p>

The component `<Link>` can also receive an URL object and it will automatically format it to create the URL string.
组件`<Link>`接收URL对象，而且它会自动格式化生成URL字符串

```jsx
// pages/index.js
import Link from 'next/link'

export default () =>
  <div>
    Click{' '}
    <Link href={{ pathname: '/about', query: { name: 'Zeit' } }}>
      <a>here</a>
    </Link>{' '}
    to read more
  </div>
```

将生成URL字符串`/about?name=Zeit`，你可以使用任何在[Node.js URL module documentation](https://nodejs.org/api/url.html#url_url_strings_and_url_objects)定义过的属性。


##### Replace instead of push url
##### 替换路由

The default behaviour for the `<Link>` component is to `push` a new url into the stack. You can use the `replace` prop to prevent adding a new entry.

```jsx
// pages/index.js
import Link from 'next/link'

export default () =>
  <div>
    Click{' '}
    <Link href="/about" replace>
      <a>here</a>
    </Link>{' '}
    to read more
  </div>
```

##### Using a component that supports `onClick`

`<Link>` supports any component that supports the `onClick` event. In case you don't provide an `<a>` tag, it will only add the `onClick` event handler and won't pass the `href` property.

```jsx
// pages/index.js
import Link from 'next/link'

export default () =>
  <div>
    Click{' '}
    <Link href="/about">
      <img src="/static/image.png" alt="image" />
    </Link>
  </div>
```

##### Forcing the Link to expose `href` to its child

If child is an `<a>` tag and doesn't have a href attribute we specify it so that the repetition is not needed by the user. However, sometimes, you’ll want to pass an `<a>` tag inside of a wrapper and the `Link` won’t recognize it as a *hyperlink*, and, consequently, won’t transfer its `href` to the child. In cases like that, you should define a boolean `passHref` property to the `Link`, forcing it to expose its `href` property to the child.

**Please note**: using a tag other than `a` and failing to pass `passHref` may result in links that appear to navigate correctly, but, when being crawled by search engines, will not be recognized as links (owing to the lack of `href` attribute). This may result in negative effects on your sites SEO.

```jsx
import Link from 'next/link'
import Unexpected_A from 'third-library'

export default ({ href, name }) =>
  <Link href={href} passHref>
    <Unexpected_A>
      {name}
    </Unexpected_A>
  </Link>
```

##### Disabling the scroll changes to top on page
##### 禁止滚动到页面顶部

`<Link>`的默认行为就是滚到页面顶部。当有hash定义时（＃），页面将会滚动到对应的id上，就像`<a>`标签一样。为了预防滚动到顶部，可以给`<Link>`加
`scroll={false}`属性：

```jsx
<Link scroll={false} href="/?counter=10"><a>Disables scrolling</a></Link>
<Link href="/?counter=10"><a>Changes with scrolling to top</a></Link>
```

#### Imperatively
#### 命令式

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/using-router">Basic routing</a></li>
    <li><a href="./examples/with-loading">With a page loading indicator</a></li>
  </ul>
</details></p>

You can also do client-side page transitions using the `next/router`
你也可以用`next/router`实现客户端路由切换


```jsx
import Router from 'next/router'

export default () =>
  <div>
    Click <span onClick={() => Router.push('/about')}>here</span> to read more
  </div>
```

#### Intercepting `popstate`
#### 拦截器 `popstate`

In some cases (for example, if using a [custom router](#custom-server-and-routing)), you may wish
to listen to [`popstate`](https://developer.mozilla.org/en-US/docs/Web/Events/popstate) and react before the router acts on it.
有些情况（比如使用[custom router](#custom-server-and-routing)），你可能想监听[`popstate`](https://developer.mozilla.org/en-US/docs/Web/Events/popstate)，在路由跳转前做一些动作。
For example, you could use this to manipulate the request, or force an SSR refresh.
比如，你可以操作request或强制SSR刷新

```jsx
import Router from 'next/router'

Router.beforePopState(({ url, as, options }) => {
  // I only want to allow these two routes!
  if (as !== "/" || as !== "/other") {
    // Have SSR render bad routes as a 404.
    window.location.href = as
    return false
  }

  return true
});
```

If you return a falsy value from `beforePopState`, `Router` will not handle `popstate`;
you'll be responsible for handling it, in that case.
See [Disabling File-System Routing](#disabling-file-system-routing).
如果你在`beforePopState`中返回false，`Router`将不会执行`popstate`事件。

Above `Router` object comes with the following API:
以上`Router`对象的API如下：

- `route` - `String` of the current route
- `route` - 当前路由的`String`类型
- `pathname` - `String` of the current path excluding the query string
- `pathname` - 不包含查询内容的当前路径，为`String`类型
- `query` - 查询内容，被解析成`Object`类型. 默认为`{}`
- `asPath` - 展现在浏览器上的实际路径，包含查询内容，为`String`类型
- `push(url, as=url)` - performs a `pushState` call with the given url
- `push(url, as=url)` - 页面渲染第一个参数url的页面，浏览器栏显示的是第二个参数url
- `replace(url, as=url)` - performs a `replaceState` call with the given url
- `beforePopState(cb=function)` - intercept popstate before router processes the event.
- `beforePopState(cb=function)` - 在路由器处理事件之前拦截.

The second `as` parameter for `push` and `replace` is an optional _decoration_ of the URL. Useful if you configured custom routes on the server.
`push` 和 `replace` 函数的第二个参数`as`，是为了装饰URL作用。如果你在服务器端设置了自定义路由将会起作用。

_Note: in order to programmatically change the route without triggering navigation and component-fetching, use `props.url.push` and `props.url.replace` within a component_
注意：为了用编程方式，而不是用导航栏触发或组件获取的方式来切换路由，可以在组件里使用`props.url.push` 或 `props.url.replace`。

##### With URL object
##### URL对象作用

You can use an URL object the same way you use it in a `<Link>` component to `push` and `replace` an URL.
`push` 或 `replace`可接收的URL对象（`<Link>`组件的URL对象一样）来生成URL。

```jsx
import Router from 'next/router'

const handler = () =>
  Router.push({
    pathname: '/about',
    query: { name: 'Zeit' }
  })

export default () =>
  <div>
    Click <span onClick={handler}>here</span> to read more
  </div>
```

This uses the same exact parameters as in the `<Link>` component.
也可以像`<Link>`组件一样添加额外的参数。

##### Router Events
##### 路由事件

You can also listen to different events happening inside the Router.
Here's a list of supported events:
你可以监听路由相关事件。
下面是事件支持列表：

- `routeChangeStart(url)` - Fires when a route starts to change
- `routeChangeStart(url)` - 路由开始切换时触发
- `routeChangeComplete(url)` - Fires when a route changed completely
- `routeChangeComplete(url)` - 完成路由切换时触发
- `routeChangeError(err, url)` - Fires when there's an error when changing routes
- `routeChangeError(err, url)` - 路由切换报错时触发
- `beforeHistoryChange(url)` - Fires just before changing the browser's history
- `beforeHistoryChange(url)` - 浏览器history模式开始切换时触发
- `hashChangeStart(url)` - Fires when the hash will change but not the page
- `hashChangeStart(url)` - 开始切换hash值但是没有切换页面路由时触发
- `hashChangeComplete(url)` - Fires when the hash has changed but not the page
- `hashChangeComplete(url)` - 完成切换hash值但是没有切换页面路由时触发

> Here `url` is the URL shown in the browser. If you call `Router.push(url, as)` (or similar), then the value of `url` will be `as`.
> 这里的`url`是指显示在浏览器中的url。如果你用了`Router.push(url, as)`（或类似的方法），那浏览器中的url将会显示as的值。

Here's how to properly listen to the router event `routeChangeStart`:
下面是如何正确使用路由事件`routeChangeStart`的例子：

```js
const handleRouteChange = url => {
  console.log('App is changing to: ', url)
}

Router.events.on('routeChangeStart', handleRouteChange)
```

If you no longer want to listen to that event, you can unsubscribe with the `off` method:
如果你不想长期监听该事件，你可以用`off`事件去取消监听：

```js
Router.events.off('routeChangeStart', handleRouteChange)
```

If a route load is cancelled (for example by clicking two links rapidly in succession), `routeChangeError` will fire. The passed `err` will contain a `cancelled` property set to `true`.
如果路由加载被取消（比如快速连续双击链接）

```js
Router.events.on('routeChangeError', (err, url) => {
  if (err.cancelled) {
    console.log(`Route to ${url} was cancelled!`)
  }
})
```

##### Shallow Routing
##### 浅层路由

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/with-shallow-routing">Shallow Routing</a></li>
  </ul>
</details></p>

Shallow routing allows you to change the URL without running `getInitialProps`. You'll receive the updated `pathname` and the `query` via the `url` prop of the same page that's loaded, without losing state.
浅层路由允许你改变URL但是不执行 `getInitialProps`生命周期。你可以加载相同页面的URL，得到更新后的路由属性`pathname`和`query`，并不失去state状态。

You can do this by invoking either `Router.push` or `Router.replace` with the `shallow: true` option. Here's an example:
你可以给`Router.push` 或 `Router.replace`方法加`shallow: true`参数。如下面的例子所示：

```js
// Current URL is "/"
const href = '/?counter=10'
const as = href
Router.push(href, as, { shallow: true })
```

Now, the URL is updated to `/?counter=10`. You can see the updated URL with `this.props.url` inside the `Component`.
现在URL更新为`/?counter=10`。在组件里查看`this.props.url`你将会看到更新的URL。

You can watch for URL changes via [`componentWillReceiveProps`](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops) hook as shown below:
你可以在[`componentWillReceiveProps`](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops)钩子函数中监听URL的变化。

```js
componentWillReceiveProps(nextProps) {
  const { pathname, query } = nextProps.url
  // fetch data based on the new query
}
```

> NOTES:
>
> Shallow routing works **only** for same page URL changes. For an example, let's assume we have another page called `about`, and you run this:
> ```js
> Router.push('/?counter=10', '/about?counter=10', { shallow: true })
> ```
> Since that's a new page, it'll unload the current page, load the new one and call `getInitialProps` even though we asked to do shallow routing.

> 注意:
>
> 浅层路由只作用于相同URL的参数改变，比如我们假定有个其他路由`about`，而你向下面代码样运行:
> ```js
> Router.push('/?counter=10', '/about?counter=10', { shallow: true })
> ```
> 那么这将会出现新页面，即使我们加了浅层路由，但是它还是会卸载当前页，会加载新的页面并触发新页面的`getInitialProps`。

#### Using a Higher Order Component
#### 高阶组件

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/using-with-router">Using the `withRouter` utility</a></li>
  </ul>
</details></p>

If you want to access the `router` object inside any component in your app, you can use the `withRouter` Higher-Order Component. Here's how to use it:
如果你想应用里每个组件都处理路由对象，你可以使用`withRouter`高阶组件。下面是如何使用它：

```jsx
import { withRouter } from 'next/router'

const ActiveLink = ({ children, router, href }) => {
  const style = {
    marginRight: 10,
    color: router.pathname === href? 'red' : 'black'
  }

  const handleClick = (e) => {
    e.preventDefault()
    router.push(href)
  }

  return (
    <a href={href} onClick={handleClick} style={style}>
      {children}
    </a>
  )
}

export default withRouter(ActiveLink)
```

The above `router` object comes with an API similar to [`next/router`](#imperatively).
上面路由对象的API可以参考[`next/router`](#imperatively).

### Prefetching Pages
### 预加载页面

⚠️ 只有生产环境才有此功能 ⚠️

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-prefetching">Prefetching</a></li></ul>
</details></p>

Next.js has an API which allows you to prefetch pages.
Next.js有允许你预加载页面的API。

Since Next.js server-renders your pages, this allows all the future interaction paths of your app to be instant. Effectively Next.js gives you the great initial download performance of a _website_, with the ahead-of-time download capabilities of an _app_. [Read more](https://zeit.co/blog/next#anticipation-is-the-key-to-performance).

用Next.js服务端渲染你的页面，可以达到所有你应用里所有未来会跳转的路径即时响应，有效的应用Next.js，可以通过预加载应用程序的功能，最大程度的初始化网站性能。

> With prefetching Next.js only downloads JS code. When the page is getting rendered, you may need to wait for the data.
> Next.js的预加载功能只预加载JS代码。当页面渲染时，你可能需要等待数据请求。

#### With `<Link>`

You can add `prefetch` prop to any `<Link>` and Next.js will prefetch those pages in the background.
你可以给<Link>添加 `prefetch` 属性，Next.js将会在后台预加载这些页面。

```jsx
import Link from 'next/link'

// example header component
export default () =>
  <nav>
    <ul>
      <li>
        <Link prefetch href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/about">
          <a>About</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/contact">
          <a>Contact</a>
        </Link>
      </li>
    </ul>
  </nav>
```

#### Imperatively
#### 命令式prefetch写法

Most prefetching needs are addressed by `<Link />`, but we also expose an imperative API for advanced usage:
大多数预加载是通过<Link />处理的，但是我们还提供了命令式API用于更复杂的场景。

```jsx
import Router from 'next/router'

export default ({ url }) =>
  <div>
    <a onClick={() => setTimeout(() => url.pushTo('/dynamic'), 100)}>
      A route transition will happen after 100ms
    </a>
    {// but we can prefetch it!
    Router.prefetch('/dynamic')}
  </div>
```

The router instance should be only used inside the client side of your app though. In order to prevent any error regarding this subject, when rendering the Router on the server side, use the imperatively prefetch method in the `componentDidMount()` lifecycle method.
路由实例只允许在应用程序的客户端。以防服务端渲染发生错误，建议prefetch事件写在`componentDidMount()`生命周期里。

```jsx
import React from 'react'
import Router from 'next/router'

export default class MyLink extends React.Component {
  componentDidMount() {
    Router.prefetch('/dynamic')
  }
  
  render() {
    return (
       <div>
        <a onClick={() => setTimeout(() => url.pushTo('/dynamic'), 100)}>
          A route transition will happen after 100ms
        </a>
      </div>   
    )
  }
}
```

### Custom server and routing
### 自定义服务端路由

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/custom-server">Basic custom server</a></li>
    <li><a href="./examples/custom-server-express">Express integration</a></li>
    <li><a href="./examples/custom-server-hapi">Hapi integration</a></li>
    <li><a href="./examples/custom-server-koa">Koa integration</a></li>
    <li><a href="./examples/parameterized-routing">Parameterized routing</a></li>
    <li><a href="./examples/ssr-caching">SSR caching</a></li>
  </ul>
</details></p>

Typically you start your next server with `next start`. It's possible, however, to start a server 100% programmatically in order to customize routes, use route patterns, etc.

When using a custom server with a server file, for example called `server.js`, make sure you update the scripts key in `package.json` to:

一般你使用 `next start` 命令来启动next服务，你还可以编写代码来自定义路由。

```json
{
  "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }
}
```

This example makes `/a` resolve to `./pages/b`, and `/b` resolve to `./pages/a`:
下面这个例子使 `/a` 路由解析为`./pages/b`，以及`/b` 路由解析为`./pages/a`;

```js
// This file doesn't go through babel or webpack transformation.
// Make sure the syntax and sources this file requires are compatible with the current node version you are running
// See https://github.com/zeit/next.js/issues/1245 for discussions on Universal Webpack or universal Babel
const { createServer } = require('http')
const { parse } = require('url')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare().then(() => {
  createServer((req, res) => {
    // Be sure to pass `true` as the second argument to `url.parse`.
    // This tells it to parse the query portion of the URL.
    const parsedUrl = parse(req.url, true)
    const { pathname, query } = parsedUrl

    if (pathname === '/a') {
      app.render(req, res, '/b', query)
    } else if (pathname === '/b') {
      app.render(req, res, '/a', query)
    } else {
      handle(req, res, parsedUrl)
    }
  }).listen(3000, err => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
```

The `next` API is as follows:
`next`的API如下所示
- `next(opts: object)`

opts的属性如下:
- `dev` (`bool`) whether to launch Next.js in dev mode - default `false`
- `dev` (`boolean`) 判断 Next.js应用是否在开发环境 - 默认 `false`
- `dir` (`string`) Next项目路径 - 默认 `'.'`
- `quiet` (`boolean`) Hide error messages containing server information - 默认 `false`
- `quiet` (`boolean`) 是否隐藏包含服务端消息在内的错误信息 - 默认 `false`
- `conf` (`object`) 与`next.config.js`的对象相同 - 默认 `{}`

Then, change your `start` script to `NODE_ENV=production node server.js`.
生产环境的话，可以更改package.json里的`start`脚本为`NODE_ENV=production node server.js`。

#### Disabling file-system routing
#### 禁止文件路由
By default, `Next` will serve each file in `/pages` under a pathname matching the filename (eg, `/pages/some-file.js` is served at `site.com/some-file`.
默认情况，`Next`将会把`/pages`下的所有文件匹配路由（如`/pages/some-file.js` 渲染为 `site.com/some-file`）

If your project uses custom routing, this behavior may result in the same content being served from multiple paths, which can present problems with SEO and UX.

To disable this behavior & prevent routing based on files in `/pages`, simply set the following option in your `next.config.js`:

```js
// next.config.js
module.exports = {
  useFileSystemPublicRoutes: false
}
```

Note that `useFileSystemPublicRoutes` simply disables filename routes from SSR; client-side routing
may still access those paths. If using this option, you should guard against navigation to routes
you do not want programmatically.

You may also wish to configure the client-side Router to disallow client-side redirects to filename
routes; please refer to [Intercepting `popstate`](#intercepting-popstate).

#### Dynamic assetPrefix

Sometimes we need to set the `assetPrefix` dynamically. This is useful when changing the `assetPrefix` based on incoming requests.
For that, we can use `app.setAssetPrefix`.

Here's an example usage of it:

```js
const next = require('next')
const micro = require('micro')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handleNextRequests = app.getRequestHandler()

app.prepare().then(() => {
  const server = micro((req, res) => {
    // Add assetPrefix support based on the hostname
    if (req.headers.host === 'my-app.com') {
      app.setAssetPrefix('http://cdn.com/myapp')
    } else {
      app.setAssetPrefix('')
    }

    handleNextRequests(req, res)
  })

  server.listen(port, (err) => {
    if (err) {
      throw err
    }

    console.log(`> Ready on http://localhost:${port}`)
  })
})

```

### Dynamic Import

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/with-dynamic-import">With Dynamic Import</a></li>
  </ul>
</details></p>

Next.js supports TC39 [dynamic import proposal](https://github.com/tc39/proposal-dynamic-import) for JavaScript.
With that, you could import JavaScript modules (inc. React Components) dynamically and work with them.

You can think dynamic imports as another way to split your code into manageable chunks.
Since Next.js supports dynamic imports with SSR, you could do amazing things with it.

Here are a few ways to use dynamic imports.

#### 1. Basic Usage (Also does SSR)

```jsx
import dynamic from 'next/dynamic'

const DynamicComponent = dynamic(import('../components/hello'))

export default () =>
  <div>
    <Header />
    <DynamicComponent />
    <p>HOME PAGE is here!</p>
  </div>
```

#### 2. With Custom Loading Component

```jsx
import dynamic from 'next/dynamic'

const DynamicComponentWithCustomLoading = dynamic(
  import('../components/hello2'),
  {
    loading: () => <p>...</p>
  }
)

export default () =>
  <div>
    <Header />
    <DynamicComponentWithCustomLoading />
    <p>HOME PAGE is here!</p>
  </div>
```

#### 3. With No SSR

```jsx
import dynamic from 'next/dynamic'

const DynamicComponentWithNoSSR = dynamic(import('../components/hello3'), {
  ssr: false
})

export default () =>
  <div>
    <Header />
    <DynamicComponentWithNoSSR />
    <p>HOME PAGE is here!</p>
  </div>
```

#### 4. With Multiple Modules At Once

```jsx
import dynamic from 'next/dynamic'

const HelloBundle = dynamic({
  modules: () => {
    const components = {
      Hello1: import('../components/hello1'),
      Hello2: import('../components/hello2')
    }

    return components
  },
  render: (props, { Hello1, Hello2 }) =>
    <div>
      <h1>
        {props.title}
      </h1>
      <Hello1 />
      <Hello2 />
    </div>
})

export default () => <HelloBundle title="Dynamic Bundle" />
```

### Custom `<App>`

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-app-layout">Using `_app.js` for layout</a></li></ul>
  <ul><li><a href="./examples/with-componentdidcatch">Using `_app.js` to override `componentDidCatch`</a></li></ul>
</details></p>

Next.js uses the `App` component to initialize pages. You can override it and control the page initialization. Which allows you to do amazing things like:

- Persisting layout between page changes
- Keeping state when navigating pages
- Custom error handling using `componentDidCatch`
- Inject additional data into pages (for example by processing GraphQL queries)

To override, create the `./pages/_app.js` file and override the App class as shown below:

```js
import App, {Container} from 'next/app'
import React from 'react'

export default class MyApp extends App {
  static async getInitialProps ({ Component, router, ctx }) {
    let pageProps = {}

    if (Component.getInitialProps) {
      pageProps = await Component.getInitialProps(ctx)
    }

    return {pageProps}
  }

  render () {
    const {Component, pageProps} = this.props
    return <Container>
      <Component {...pageProps} />
    </Container>
  }
}
```

### Custom `<Document>`

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-styled-components">Styled components custom document</a></li></ul>
  <ul><li><a href="./examples/with-amp">Google AMP</a></li></ul>
</details></p>

- Is rendered on the server side
- Is used to change the initial server side rendered document markup
- Commonly used to implement server side rendering for css-in-js libraries like [styled-components](./examples/with-styled-components), [glamorous](./examples/with-glamorous) or [emotion](with-emotion). [styled-jsx](https://github.com/zeit/styled-jsx) is included with Next.js by default.

Pages in `Next.js` skip the definition of the surrounding document's markup. For example, you never include `<html>`, `<body>`, etc. To override that default behavior, you must create a file at `./pages/_document.js`, where you can extend the `Document` class:

```jsx
// _document is only rendered on the server side and not on the client side
// Event handlers like onClick can't be added to this file

// ./pages/_document.js
import Document, { Head, Main, NextScript } from 'next/document'

export default class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const initialProps = await Document.getInitialProps(ctx)
    return { ...initialProps }
  }

  render() {
    return (
      <html>
        <Head>
          <style>{`body { margin: 0 } /* custom! */`}</style>
        </Head>
        <body className="custom_class">
          <Main />
          <NextScript />
        </body>
      </html>
    )
  }
}
```

The `ctx` object is equivalent to the one received in all [`getInitialProps`](#fetching-data-and-component-lifecycle) hooks, with one addition:

- `renderPage` (`Function`) a callback that executes the actual React rendering logic (synchronously). It's useful to decorate this function in order to support server-rendering wrappers like Aphrodite's [`renderStatic`](https://github.com/Khan/aphrodite#server-side-rendering)

__Note: React-components outside of `<Main />` will not be initialised by the browser. Do _not_ add application logic here. If you need shared components in all your pages (like a menu or a toolbar), take a look at the `App` component instead.__

### Custom error handling

404 or 500 errors are handled both client and server side by a default component `error.js`. If you wish to override it, define a `_error.js` in the pages folder:

```jsx
import React from 'react'

export default class Error extends React.Component {
  static getInitialProps({ res, err }) {
    const statusCode = res ? res.statusCode : err ? err.statusCode : null;
    return { statusCode }
  }

  render() {
    return (
      <p>
        {this.props.statusCode
          ? `An error ${this.props.statusCode} occurred on server`
          : 'An error occurred on client'}
      </p>
    )
  }
}
```

### Reusing the built-in error page

If you want to render the built-in error page you can by using `next/error`:

```jsx
import React from 'react'
import Error from 'next/error'
import fetch from 'isomorphic-unfetch'

export default class Page extends React.Component {
  static async getInitialProps() {
    const res = await fetch('https://api.github.com/repos/zeit/next.js')
    const statusCode = res.statusCode > 200 ? res.statusCode : false
    const json = await res.json()

    return { statusCode, stars: json.stargazers_count }
  }

  render() {
    if (this.props.statusCode) {
      return <Error statusCode={this.props.statusCode} />
    }

    return (
      <div>
        Next stars: {this.props.stars}
      </div>
    )
  }
}
```

> If you have created a custom error page you have to import your own `_error` component instead of `next/error`

### Custom configuration

For custom advanced behavior of Next.js, you can create a `next.config.js` in the root of your project directory (next to `pages/` and `package.json`).

Note: `next.config.js` is a regular Node.js module, not a JSON file. It gets used by the Next server and build phases, and not included in the browser build.

```js
// next.config.js
module.exports = {
  /* config options here */
}
```

Or use a function:

```js
module.exports = (phase, {defaultConfig}) => {
  //
  // https://github.com/zeit/
  return {
    /* config options here */
  }
}
```

`phase` is the current context in which the configuration is loaded. You can see all phases here: [constants](./lib/constants.js)
Phases can be imported from `next/constants`:

```js
const {PHASE_DEVELOPMENT_SERVER} = require('next/constants')
module.exports = (phase, {defaultConfig}) => {
  if(phase === PHASE_DEVELOPMENT_SERVER) {
    return {
      /* development only config options here */
    }
  }

  return {
    /* config options for all phases except development here */
  }
}
```

#### Setting a custom build directory

You can specify a name to use for a custom build directory. For example, the following config will create a `build` folder instead of a `.next` folder. If no configuration is specified then next will create a `.next` folder.

```js
// next.config.js
module.exports = {
  distDir: 'build'
}
```

#### Disabling etag generation

You can disable etag generation for HTML pages depending on your cache strategy. If no configuration is specified then Next will generate etags for every page.

```js
// next.config.js
module.exports = {
  generateEtags: false
}
```

#### Configuring the onDemandEntries

Next exposes some options that give you some control over how the server will dispose or keep in memories pages built:

```js
module.exports = {
  onDemandEntries: {
    // period (in ms) where the server will keep pages in the buffer
    maxInactiveAge: 25 * 1000,
    // number of pages that should be kept simultaneously without being disposed
    pagesBufferLength: 2,
  }
}
```

This is development-only feature. If you want to cache SSR pages in production, please see [SSR-caching](https://github.com/zeit/next.js/tree/canary/examples/ssr-caching) example.

#### Configuring extensions looked for when resolving pages in `pages`

Aimed at modules like [`@zeit/next-typescript`](https://github.com/zeit/next-plugins/tree/master/packages/next-typescript), that add support for pages ending in `.ts`. `pageExtensions` allows you to configure the extensions looked for in the `pages` directory when resolving pages.

```js
// next.config.js
module.exports = {
  pageExtensions: ['jsx', 'js']
}
```

#### Configuring the build ID

Next.js uses a constant generated at build time to identify which version of your application is being served. This can cause problems in multi-server deployments when `next build` is ran on every server. In order to keep a static build id between builds you can provide the `generateBuildId` function:

```js
// next.config.js
module.exports = {
  generateBuildId: async () => {
    // For example get the latest git commit hash here
    return 'my-build-id'
  }
}
```

### Customizing webpack config

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-webpack-bundle-analyzer">Custom webpack bundle analyzer</a></li></ul>
</details></p>

Some commonly asked for features are available as modules:

- [@zeit/next-css](https://github.com/zeit/next-plugins/tree/master/packages/next-css)
- [@zeit/next-sass](https://github.com/zeit/next-plugins/tree/master/packages/next-sass)
- [@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)
- [@zeit/next-preact](https://github.com/zeit/next-plugins/tree/master/packages/next-preact)
- [@zeit/next-typescript](https://github.com/zeit/next-plugins/tree/master/packages/next-typescript)

*Warning: The `webpack` function is executed twice, once for the server and once for the client. This allows you to distinguish between client and server configuration using the `isServer` property*

Multiple configurations can be combined together with function composition. For example:

```js
const withTypescript = require('@zeit/next-typescript')
const withSass = require('@zeit/next-sass')

module.exports = withTypescript(withSass({
  webpack(config, options) {
    // Further custom configuration here
    return config
  }
}))
```

In order to extend our usage of `webpack`, you can define a function that extends its config via `next.config.js`.

```js
// next.config.js is not transformed by Babel. So you can only use javascript features supported by your version of Node.js.

module.exports = {
  webpack: (config, { buildId, dev, isServer, defaultLoaders }) => {
    // Perform customizations to webpack config
    // Important: return the modified config
    return config
  },
  webpackDevMiddleware: config => {
    // Perform customizations to webpack dev middleware config
    // Important: return the modified config
    return config
  }
}
```

The second argument to `webpack` is an object containing properties useful when customing the WebPack configuration:

- `buildId` - `String` the build id used as a unique identifier between builds
- `dev` - `Boolean` shows if the compilation is done in development mode
- `isServer` - `Boolean` shows if the resulting configuration will be used for server side (`true`), or client size compilation (`false`).
- `defaultLoaders` - `Object` Holds loader objects Next.js uses internally, so that you can use them in custom configuration
  - `babel` - `Object` the `babel-loader` configuration for Next.js.
  - `hotSelfAccept` - `Object` the `hot-self-accept-loader` configuration. This loader should only be used for advanced use cases. For example [`@zeit/next-typescript`](https://github.com/zeit/next-plugins/tree/master/packages/next-typescript) adds it for top-level typescript pages.

Example usage of `defaultLoaders.babel`: 

```js
// Example next.config.js for adding a loader that depends on babel-loader
// This source was taken from the @zeit/next-mdx plugin source: 
// https://github.com/zeit/next-plugins/blob/master/packages/next-mdx
module.exports = {
  webpack: (config, {}) => {
    config.module.rules.push({
      test: /\.mdx/,
      use: [
        options.defaultLoaders.babel,
        {
          loader: '@mdx-js/loader',
          options: pluginOptions.options
        }
      ]
    })

    return config
  }
}
```

### Customizing babel config

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-custom-babel-config">Custom babel configuration</a></li></ul>
</details></p>

In order to extend our usage of `babel`, you can simply define a `.babelrc` file at the root of your app. This file is optional.

If found, we're going to consider it the *source of truth*, therefore it needs to define what next needs as well, which is the `next/babel` preset.

This is designed so that you are not surprised by modifications we could make to the babel configurations.

Here's an example `.babelrc` file:

```json
{
  "presets": ["next/babel"],
  "plugins": []
}
```

The `next/babel` preset includes everything needed to transpile React applications. This includes:

- preset-env
- preset-react
- plugin-proposal-class-properties
- plugin-proposal-object-rest-spread
- plugin-transform-runtime
- styled-jsx

These presets / plugins **should not** be added to your custom `.babelrc`. Instead, you can configure them on the `next/babel` preset:

```json
{
  "presets": [
    ["next/babel", {
      "preset-env": {},
      "transform-runtime": {},
      "styled-jsx": {},
      "class-properties": {}
    }]
  ],
  "plugins": []
}
```

The `modules` option on `"preset-env"` should be kept to `false` otherwise webpack code splitting is disabled.

### Exposing configuration to the server / client side

The `next/config` module gives your app access to runtime configuration stored in your `next.config.js`. Place any server-only runtime config under a `serverRuntimeConfig` property and anything accessible to both client and server-side code under `publicRuntimeConfig`.

```js
// next.config.js
module.exports = {
  serverRuntimeConfig: { // Will only be available on the server side
    mySecret: 'secret'
  },
  publicRuntimeConfig: { // Will be available on both server and client
    staticFolder: '/static',
    mySecret: process.env.MY_SECRET // Pass through env variables
  }
}
```

```js
// pages/index.js
import getConfig from 'next/config'
// Only holds serverRuntimeConfig and publicRuntimeConfig from next.config.js nothing else.
const {serverRuntimeConfig, publicRuntimeConfig} = getConfig()

console.log(serverRuntimeConfig.mySecret) // Will only be available on the server side
console.log(publicRuntimeConfig.staticFolder) // Will be available on both server and client

export default () => <div>
  <img src={`${publicRuntimeConfig.staticFolder}/logo.png`} alt="logo" />
</div>
```

### Starting the server on alternative hostname

To start the development server using a different default hostname you can use `--hostname hostname_here` or `-H hostname_here` option with next dev. This will start a TCP server listening for connections on the provided host.

### CDN support with Asset Prefix

To set up a CDN, you can set up the `assetPrefix` setting and configure your CDN's origin to resolve to the domain that Next.js is hosted on.

```js
const isProd = process.env.NODE_ENV === 'production'
module.exports = {
  // You may only need to add assetPrefix in the production.
  assetPrefix: isProd ? 'https://cdn.mydomain.com' : ''
}
```

Note: Next.js will automatically use that prefix in the scripts it loads, but this has no effect whatsoever on `/static`. If you want to serve those assets over the CDN, you'll have to introduce the prefix yourself. One way of introducing a prefix that works inside your components and varies by environment is documented [in this example](https://github.com/zeit/next.js/tree/master/examples/with-universal-configuration).

## Production deployment

To deploy, instead of running `next`, you want to build for production usage ahead of time. Therefore, building and starting are separate commands:

```bash
next build
next start
```

For example, to deploy with [`now`](https://zeit.co/now) a `package.json` like follows is recommended:

```json
{
  "name": "my-app",
  "dependencies": {
    "next": "latest"
  },
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

Then run `now` and enjoy!

Next.js can be deployed to other hosting solutions too. Please have a look at the ['Deployment'](https://github.com/zeit/next.js/wiki/Deployment) section of the wiki.

Note: `NODE_ENV` is properly configured by the `next` subcommands, if absent, to maximize performance. if you’re using Next.js [programmatically](#custom-server-and-routing), it’s your responsibility to set `NODE_ENV=production` manually!

Note: we recommend putting `.next`, or your [custom dist folder](https://github.com/zeit/next.js#custom-configuration), in `.gitignore` or `.npmignore`. Otherwise, use `files` or `now.files` to opt-into a whitelist of files you want to deploy, excluding `.next` or your custom dist folder.

## Browser support

Next.js supports IE11 and all modern browsers out of the box using [`@babel/preset-env`](https://new.babeljs.io/docs/en/next/babel-preset-env.html) without polyfills. In cases where your own code or any external NPM dependencies you are using requires features not supported by your target browsers you will need to implement polyfills. 

The [polyfills](https://github.com/zeit/next.js/tree/canary/examples/with-polyfills) example demonstrates the recommended approach to implement polyfills.

## Static HTML export

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-static-export">Static export</a></li></ul>
</details></p>

`next export` is a way to run your Next.js app as a standalone static app without the need for a Node.js server.
The exported app supports almost every feature of Next.js, including dynamic urls, prefetching, preloading and dynamic imports.

The way `next export` works is by pre-rendering all pages possible to HTML. It does so based on a mapping of `pathname` key to page object. This mapping is called the `exportPathMap`.

The page object has 2 values:

- `page` - `String` the page inside the `pages` directory to render
- `query` - `Object` the `query` object passed to `getInitialProps` when pre-rendering. Defaults to `{}`


### Usage

Simply develop your app as you normally do with Next.js. Then run:

```
next build
next export
```

By default `next export` doesn't require any configuration. It will generate a default `exportPathMap` containing the routes to pages inside the `pages` directory. 

If your application has dynamic routes you can add a dynamic `exportPathMap` in `next.config.js`.
This function is asynchronous and gets the default `exportPathMap` as a parameter.

```js
// next.config.js
module.exports = {
  exportPathMap: async function (defaultPathMap) {
    return {
      '/': { page: '/' },
      '/about': { page: '/about' },
      '/readme.md': { page: '/readme' },
      '/p/hello-nextjs': { page: '/post', query: { title: 'hello-nextjs' } },
      '/p/learn-nextjs': { page: '/post', query: { title: 'learn-nextjs' } },
      '/p/deploy-nextjs': { page: '/post', query: { title: 'deploy-nextjs' } }
    }
  }
}
```

> Note that if the path ends with a directory, it will be exported as `/dir-name/index.html`, but if it ends with an extension, it will be exported as the specified filename, e.g. `/readme.md` above. If you use a file extension other than `.html`, you may need to set the `Content-Type` header to `text/html` when serving this content.

Then simply run these commands:

```sh
next build
next export
```

For that you may need to add a NPM script to `package.json` like this:

```json
{
  "scripts": {
    "build": "next build",
    "export": "npm run build && next export"
  }
}
```

And run it at once with:

```sh
npm run export
```

Then you have a static version of your app in the `out` directory.

> You can also customize the output directory. For that run `next export -h` for the help.

Now you can deploy the `out` directory to any static hosting service. Note that there is an additional step for deploying to GitHub Pages, [documented here](https://github.com/zeit/next.js/wiki/Deploying-a-Next.js-app-into-GitHub-Pages).

For an example, simply visit the `out` directory and run following command to deploy your app to [ZEIT Now](https://zeit.co/now).

```sh
now
```

### Limitation

With `next export`, we build a HTML version of your app. At export time we will run `getInitialProps` of your pages.

The `req` and `res` fields of the `context` object passed to `getInitialProps` are not available as there is no server running.

> You won't be able to render HTML dynamically when static exporting, as we pre-build the HTML files. If you want to do dynamic rendering use `next start` or the custom server API

## Multi Zones

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-zones">With Zones</a></li></ul>
</details></p>

A zone is a single deployment of a Next.js app. Just like that, you can have multiple zones. Then you can merge them as a single app.

For an example, you can have two zones like this:

* https://docs.my-app.com for serving `/docs/**`
* https://ui.my-app.com for serving all other pages

With multi zones support, you can merge both these apps into a single one. Which allows your customers to browse it using a single URL. But you can develop and deploy both apps independently.

> This is exactly the same concept as microservices, but for frontend apps.

### How to define a zone

There are no special zones related APIs. You only need to do following things:

* Make sure to keep only the pages you need in your app. (For an example, https://ui.my-app.com should not contain pages for `/docs/**`)
* Make sure your app has an [assetPrefix](https://github.com/zeit/next.js#cdn-support-with-asset-prefix). (You can also define the assetPrefix [dynamically](https://github.com/zeit/next.js#dynamic-assetprefix).)

### How to merge them

You can merge zones using any HTTP proxy.

You can use [micro proxy](https://github.com/zeit/micro-proxy) as your local proxy server. It allows you to easily define routing rules like below:

```json
{
  "rules": [
    {"pathname": "/docs**", "method":["GET", "POST", "OPTIONS"], "dest": "https://docs.my-app.com"},
    {"pathname": "/**", "dest": "https://ui.my-app.com"}
  ]
}
```

For the production deployment, you can use the [path alias](https://zeit.co/docs/features/path-aliases) feature if you are using [ZEIT now](https://zeit.co/now). Otherwise, you can configure your existing proxy server to route HTML pages using a set of rules as show above.

## Recipes

- [Setting up 301 redirects](https://www.raygesualdo.com/posts/301-redirects-with-nextjs/)
- [Dealing with SSR and server only modules](https://arunoda.me/blog/ssr-and-server-only-modules)
- [Building with React-Material-UI-Next-Express-Mongoose-Mongodb](https://github.com/builderbook/builderbook)
- [Build a SaaS Product with React-Material-UI-Next-MobX-Express-Mongoose-MongoDB-TypeScript](https://github.com/async-labs/saas)

## FAQ

<details>
  <summary>Is this production ready?</summary>
  Next.js has been powering https://zeit.co since its inception.

  We’re ecstatic about both the developer experience and end-user performance, so we decided to share it with the community.
</details>

<details>
  <summary>How big is it?</summary>

The client side bundle size should be measured in a per-app basis.
A small Next main bundle is around 65kb gzipped.

</details>

<details>
  <summary>Is this like `create-react-app`?</summary>

Yes and No.

Yes in that both make your life easier.

No in that it enforces a _structure_ so that we can do more advanced things like:
- Server side rendering
- Automatic code splitting

In addition, Next.js provides two built-in features that are critical for every single website:
- Routing with lazy component loading: `<Link>` (by importing `next/link`)
- A way for components to alter `<head>`: `<Head>` (by importing `next/head`)

If you want to create re-usable React components that you can embed in your Next.js app or other React applications, using `create-react-app` is a great idea. You can later `import` it and keep your codebase clean!

</details>

<details>
  <summary>How do I use CSS-in-JS solutions?</summary>

Next.js bundles [styled-jsx](https://github.com/zeit/styled-jsx) supporting scoped css. However you can use any CSS-in-JS solution in your Next app by just including your favorite library [as mentioned before](#css-in-js) in the document.
</details>

<details>
  <summary>What syntactic features are transpiled? How do I change them?</summary>

We track V8. Since V8 has wide support for ES6 and `async` and `await`, we transpile those. Since V8 doesn’t support class decorators, we don’t transpile those.

See [this](https://github.com/zeit/next.js/blob/master/server/build/webpack.js#L79) and [this](https://github.com/zeit/next.js/issues/26)

</details>

<details>
  <summary>Why a new Router?</summary>

Next.js is special in that:

- Routes don’t need to be known ahead of time
- Routes are always lazy-loadable
- Top-level components can define `getInitialProps` that should _block_ the loading of the route (either when server-rendering or lazy-loading)

As a result, we were able to introduce a very simple approach to routing that consists of two pieces:

- Every top level component receives a `url` object to inspect the url or perform modifications to the history
- A `<Link />` component is used to wrap elements like anchors (`<a/>`) to perform client-side transitions

We tested the flexibility of the routing with some interesting scenarios. For an example, check out [nextgram](https://github.com/zeit/nextgram).

</details>

<details>
<summary>How do I define a custom fancy route?</summary>

We [added](#custom-server-and-routing) the ability to map between an arbitrary URL and any component by supplying a request handler.

On the client side, we have a parameter call `as` on `<Link>` that _decorates_ the URL differently from the URL it _fetches_.
</details>

<details>
<summary>How do I fetch data?</summary>

It’s up to you. `getInitialProps` is an `async` function (or a regular function that returns a `Promise`). It can retrieve data from anywhere.
</details>

<details>
  <summary>Can I use it with GraphQL?</summary>

Yes! Here's an example with [Apollo](./examples/with-apollo).

</details>

<details>
<summary>Can I use it with Redux?</summary>

Yes! Here's an [example](./examples/with-redux)
</details>

<details>
<summary>Can I use Next with my favorite Javascript library or toolkit?</summary>

Since our first release we've had **many** example contributions, you can check them out in the [examples](./examples) directory
</details>

<details>
<summary>What is this inspired by?</summary>

Many of the goals we set out to accomplish were the ones listed in [The 7 principles of Rich Web Applications](http://rauchg.com/2014/7-principles-of-rich-web-applications/) by Guillermo Rauch.

The ease-of-use of PHP is a great inspiration. We feel Next.js is a suitable replacement for many scenarios where you otherwise would use PHP to output HTML.

Unlike PHP, we benefit from the ES6 module system and every file exports a **component or function** that can be easily imported for lazy evaluation or testing.

As we were researching options for server-rendering React that didn’t involve a large number of steps, we came across [react-page](https://github.com/facebookarchive/react-page) (now deprecated), a similar approach to Next.js by the creator of React Jordan Walke.

</details>

## Contributing

Please see our [contributing.md](./contributing.md)

## Authors

- Arunoda Susiripala ([@arunoda](https://twitter.com/arunoda)) – [ZEIT](https://zeit.co)
- Tim Neutkens ([@timneutkens](https://twitter.com/timneutkens)) – [ZEIT](https://zeit.co)
- Naoyuki Kanezawa ([@nkzawa](https://twitter.com/nkzawa)) – [ZEIT](https://zeit.co)
- Tony Kovanen ([@tonykovanen](https://twitter.com/tonykovanen)) – [ZEIT](https://zeit.co)
- Guillermo Rauch ([@rauchg](https://twitter.com/rauchg)) – [ZEIT](https://zeit.co)
- Dan Zajdband ([@impronunciable](https://twitter.com/impronunciable)) – Knight-Mozilla / Coral Project
