# 环境配置
```
# 使用官方脚手架创建项目
npm create vue@latest
# 一开始只需要选上Typescript支持,ESLint代码质量检测

# 启动项目,脚手架会给提示
cd vue-project
npm install #下依赖
npm run dev

# vite的命令行指令
o #用默认浏览器打开项目
```
其中输入`npm install`会在当前目录下添加`node_modules`目录,为node下载的依赖项.

## vscode插件
vue-official官方插件
# 目录结构
- public:静态资源,放ico角标,配套图片等
- `index.html`入口所有特效都在单页面内,只干了一件事:引入`/src/main.js`
- `package.json`:"script"包含`npm run`启动指令,例如`build`创建适合服务器的开发环境(多了个dist目录),`dev`创建开发实例
- `src`:包含vue文件,每个vue文件包含三部分
	1. template控制显示(类比main函数)
	2. script引入的组件(类比头文件)
	3. style样式
每个vue项目的入口都是`main.ts`中的`creatApp()`组件
# 数据双向绑定
template和script中的数据相互绑定,一个改变另外一个也随之改变
- `v-model`用于各种输入数据,可以监听输入并且随着输入改变数据的值,例如
```
<template>
	<div>
		name: <input v-model="userName"/> {{ userName }}<br />
		salary: <input type="number" v-model="salary"> {{ salary }} <br />
		<button> 提交 </button> 
</template> 
```
其中`userName`和`salary`数据需要在`<script>`部分给出
- `v-on:click`等效于`@click`,可以触发点击事件
- `v-if与v-show`若对应属性为真值,则显示元素.两者区别:
	- `v-if`会摧毁页面元素,属性为真再重新创建
	- `v-show`只控制css的display,不会摧毁元素
# options API 与 Composition API
使用`export default`的不同属性,比如`methods,data`,这些都是直接配好的,用一个统一的对象来干所有事,开发者只能配置这些给出的固定选项来控制逻辑,这种编写方式叫**options API**
在这种API模式下,不同模块用到的数据和方法都混到一起了,不易维护一个复杂的项目
### setup函数
`setup()`类比于`init()`,只在加载页面时返回一次,创建一些私有属性,返回的是某些属性的拷贝.在`<script>`标签中加`setup`可以省略掉,直接定义属性和方法.使用`ref()`包裹成一个返回对象来建立双向绑定
官方推荐使用Composition API

### ref的本质
- `ref()`返回一个双向绑定对象,使用关键字`{{}}`可以直接解析,但是在ts中应该使用`xx.value`
- 由于渲染问题,重新使用`ref()`会导致双向绑定有莫名其妙的错误,一定不要动`ref()`本身
- 使用`reactive()`封装对象类型的数据,由于`reactive()`是特殊的`proxy`类型,不需要再用`xx.value`这样的写法,直接当成对象本身用即可.使用`ref()`封装对象还是需要用`xx.value`.
- 无论`ref()`还是`reactive()`封装对象,对象的单独属性拆出来没有双向绑定能力.使用`toRef()`接口
```
const salaryInfo=reactive({userName:"liuyoudu",salary:1000})
let userName=toRef(salaryInfo.userName)
let salary = toRef(salaryInfo.salary)
```
