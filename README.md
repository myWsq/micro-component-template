# Micro Component Templete 微组件模板

> 使用此模板为微门户 (Micro Portal) 开发组件

## 基本思路

因Vue Router 支持异步组件, 将组件编译为UMD模块, 上传至CDN, 通过配置组件的链接与内存地址, 实现组件的动态变换.


## 项目设置
```shell
$ git clone https://github.com/myWsq/micro-component-templete.git
$ cd micro-component-templete
$ yarn # or npm install
```
开发前, 首先要明确当前项目的`namespace`, 一般情况下就是`package.json`的`name`字段. 根据当前的项目进行修改.

```json
// package.json
{
    "name":"foo"
    ...
}
```

一个项目可以编译出多个相互独立的微组件, 在`src/components`文件夹下新建组件并在`index.ts`中导出.

### Play

```shell
$ yarn serve
```

项目会识别`src/components/index.ts`中导出的组件并自动生成路由与导航.

### Build

```shell
$ yarn build
```
微门户会根据配置引入组件的脚本与样式文件. `{namespace}.{componentMame}` 即为组件的内存地址(path)

注意: 为了及时更新组件缓存与控制组件版本, 编译出的UMD模块文件名应含有版本号.

```shell
$ yarn build --filename foo@0.0.1
```

此方法为组件回滚机制提供了有效途径.

### Deploy

组件的发布很简单, 将编译出的UMD模块上传至CDN, 更新微门户的配置即可. 发布时, 需要填写组件的脚本文件链接, 样式文件链接(可选), 组件内存地址.

## Tip

微组件是独立开发, 独立编译, 独立部署的, 但是由于组件将嵌套在微门户内, 需要约定一些特性.

### Router

Playground中同样注入了`vue-router`, 需要注意的是, 开发阶段的路由实例与微门户环境不同, 使用`vm.$router`或`vm.$route`时需特别注意, 以微门户为主.

### 依赖

通用第三方依赖版本应与微门户使用的依赖版本相同, 使用新特性时, 需要查阅微门户依赖版本. 

## 最佳实践

fork本项目, 修改依赖与门户项目相同, 再使用自己的模板构建项目.

### 布局组件

门户项目不会渲染任何元素, 项目的布局由布局组件决定. 门户项目使用布局组件的默认slot插入页面组件. 修改布局组件时要注意: **如果出现异常情况会导致整个项目无法正常显示**.

### 如何占满页面高度

因页面组件嵌套在布局组件内, 页面组件无法通过`100vh`设置页面高度. 因此布局组件应给slot容器设置高度, 让页面组件的`height: 100%`生效. 在不定高度的情况下, 使用`flex box`可以很好的解决这个问题.

在布局组件中为slot容器设置样式:

```css
.layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  box-sizing: border-box;
  padding-top: $--height-toolbar;
  padding-left: $--width-sidebar;
}
```

在页面组件的顶级元素中指定 `flex-grow: 1`即可使元素占满剩余空间. 