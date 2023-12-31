---
title: Data Visualization - React 入门
date: 2023-08-03 14:18:17
tags: [技术,可视化,课程,编程]
index_img: /img/dvimg2.jpg
categories: 课程
---

## MVC 模型
MVC 模式代表 Model-View-Controller（模型-视图-控制器） 模式。这种模式用于应用程序的分层开发。
![](/img/mvc.png)
+ **Model** - 模型代表一个存取数据的对象，它也可以带有逻辑，在数据变化时更新控制器。
+ **View** - 视图代表模型包含的数据的可视化。
+ **Controller** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。
### 问题
应用程序状态分散，难以追踪和维护。
## React 引入
![](https://www.runoob.com/wp-content/uploads/2016/02/react.png)  
基于 Flux 架构理念，Facebook 开发了一个新的 Javascript 库，即 `React` 并于2013年5月正式开源。  
`React` 是一个用于构建用户界面的 JAVASCRIPT 库，主要用于构建 UI，`React` 拥有较高的性能，代码逻辑非常简单。
### 核心特性
+ 声明式开发
+ 组件化
+ 单项数据流
+ 函数式编程
+ 兼容其他框架
### 安装
使用 npm 包管理安装：
```bash
$ npm install -g create-react-app
```
### 使用
创建一个新的React应用
```bash
$ create-react-app react-practice
```
启动应用：
```bash
$ cd react-practice
$ npm start
```
然后，React 应用将会呈现在 `http://localhost:3000` 上。
## React 编程
### 元素
元素是构成 `React` 应用的最小单位，它用于描述屏幕上输出的内容，一个元素可以是一段 `HTML` 代码，例如：
```js
const element = <h1>Good job!</h1>;
```
可以通过 `ReactDOM` 类的 `render` 方法对元素进行渲染，首先要创建一个网页的根节点，在此节点中的所有内容都将由 `ReactDOM` 来管理：
```js
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  element,
  document.getElementById('example')
);
```
### 组件
#### 组件生命周期
组件的生命周期可分成三个状态：
+ **Mounting** (挂载)：已插入真实 DOM
    + `constructor()` 构造函数，挂载之前调用。
    + `getDerivedStateFromProps()` 在调用 `render` 方法之前调用
    + `render()`  渲染，`Class` 组件中唯一必须实现的方法。
    + `componentDidMount()` 在组件挂载后立即调用
+ **Updating** (更新)：正在被重新渲染
    + `getDerivedStateFromProps()`
    + `shouldComponentUpdate()` 当 `props` 或 `state` 发生变化时会在渲染执行之前被调用
    + `render()` 
    + `getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用
    + `componentDidMount()`
+ **Unmounting** (卸载)：已移出真实 DOM
    + `componentWillMount()` 在组件卸载及销毁之前直接调用
![](/img/lifetime.png)

#### 函数组件
函数组件的声明，其中，函数名首字母必须大写，函数返回值一般为一个 `React` 元素。
```js
function FunctionName(props) {
    return something;
}
```
使用以下语句对函数进行调用，其中，`a` 和 `b` 既是 `HTML` 元素的属性，也是函数调用时传递的参数。
```html
<FunctionName a="Cartman" b="Kenny"/>
```
函数组件的参数被传递给 `function` 的 `props` 属性。在 `function` 中，可以这样访问参数
```js
{props.a} // = "Cartman"
{props.b} // = "Kenny"
```
#### 类组件
类组件源自于 `ES6` ，与函数组件类似，他也可以接受参数并返回 `React` 元素，但与函数组件有一定区别。
```js
class ClassComp extends React.Component {
  render() {
    return something;
  }
}
```
类组件可以对组件各个生命周期所调用的方法进行自定义，但只有 `render()` 方法是必须实现的。