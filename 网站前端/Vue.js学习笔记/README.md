# Vue.js学习笔记

### jQuery与Vue.js对比
1. 做的事情

    1. jQuery

        （旧时代到现在）相对于原生JS，更好的API，兼容性极好的DOM、AJAX操作。
    1. Vue.js

        实现MVVM的数据双向绑定，实现自己的组件系统。
2. 优劣势对比

    1. jQuery

        1. 兼容性好，兼容基本所有当今浏览器；出现早，学习、使用成本低。
        2. 程序员关注DOM，频繁操作DOM；代码量较多且不好维护，当页面需求变化之后代码改动难度大。
    2. Vue.js

        1. 程序员关注数据，DOM的操作交给框架；代码清晰，利于维护；有自己的组件系统。
        2. 不兼容旧版本浏览器；需要一些学习成本。

### [Vue官方教材](https://cn.vuejs.org/v2/guide/)
1. 双向绑定，响应式。

    编写的代码不需要关注底层逻辑，只需要关注数据双向绑定。

    >Vue实现了一些智能启发式方法来最大化DOM重用。

    1. Vue实例会代理`data`、`methods`的属性可以直接修改或调用，也有以`$`开头的实例属性（如`$el`、`$data`、`$watch`）。

        只有已经被代理的内容是响应的，值的任何改变会触发视图的重新渲染。

        1. 导致试图更新：

            1. 数组的变异方法：`push`、`pop`、`shift`、`unshift`、`splice`、`sort`、`reverse`。
            2. 数组赋值。
            3. 插值：`Vue.set(数组, 索引, 新值)`。
            4. 改变数组长度：`数组.splice(新长度)`。
        2. 无法检测数组变动：

            1. 直接数组索引赋值。
            2. 直接修改数组长度。
    2. 慎用箭头函数，`this`的指向无法按预期指向Vue实例。
    3. 因为HTML不区分大小写，所以驼峰式命名的JS内容，在HTML使用时要转换为相应的短横线隔开式。

        >字符串模版、`.vue`组件，没有这些限制。
2. 模板插值

    1. 提供了完全的JS表达式支持，~~语句~~、~~流控制~~不会生效。
    2. 只能访问部分全局变量。
    3. 作用域在所属Vue实例或组件中。

    ```html
    // e.g.
    {{ number + 1 }}
    {{ ok ? 'YES' : 'NO' }}
    {{ message.split('').reverse().join('') }}
    <div v-bind:id="'list-' + id"></div>
    ```
3. 生命周期。
4. 指令

    指令（Directives）是带有`v-`前缀的DOM的特殊属性。

    1. `:`参数

        指令后的参数用`:`指明。
    2. `v-bind`绑定DOM属性与Vue实例属性

        `v-bind:`缩写：`:`。

        1. 绑定`class`

            1. `v-bind:class="{class名: Vue属性[, class名: Vue属性]}"`
            2. `v-bind:class="Vue属性"`
            3. `v-bind:class="[Vue属性[, Vue属性]]"`
            4. `v-bind:class="[Vue属性 ? Vue属性 : ''[, Vue属性]]"`
            5. `v-bind:class="[{class名: Vue属性}[, Vue属性]]"`
        2. 绑定`style`

            >自动添加样式前缀。

            1. `v-bind:style="{css属性: Vue属性[, css属性: Vue属性]}"`
            2. `v-bind:style="Vue属性"`
            3. `v-bind:style="[Vue属性[, Vue属性]]"`
            4. 多重值
    3. `v-if`、`v-else`、`v-else-if`

        `key`属性使切换的DOM不共用。

        `<template>`渲染结果不包括。
    4. `v-for="(value, key, index) in/of Vue属性/整数"`

        >比`v-if`优先级更高。

        `v-bind:key="唯一的值"`使切换的DOM不复用。

        在组件中使用`v-for`时，必须添加`v-bind:key='值'`。

        `<template>`渲染结果不包括。
    5. `v-on`事件处理

        `v-on:`缩写：`@`。

        1. `$event`时间变量
        2. 事件修饰符：

            1. `.stop`、`.prevent`、`.capture`、`.self`、`.once`
            2. `.enter`、`.tab`、`.delete`、`.esc`、`.space`、`.up`、`.down`、`.left`、`.right`、`.ctrl`、`.alt`、`.shift`、`.meta`、`.数字`
            3. `.left`、`.right`、`.middle`
            4. `.native`、`.sync`
    6. `v-model`表单

        >忽略表单元素的`value`、`checked`、`selected`等初始值，而仅通过Vue实例赋值。

        `<input>`、`<textarea>`、`<select>`

        1. 修饰符：`.lazy`、`.number`、`.trim`
    7. `v-once`一次性插值，不再响应式
    8. `v-html`输入真正HTML
    9. 修饰符：`v-on`、`v-bind`、`v-module`
    10. 过滤器：`|`
    11. `v-show`

        仅切换`display`属性。
    12. `<slot>`

        用于父向子组件插入内容。
    13. 渲染结果不包含`<template>`：`v-for`、`v-if`
5. Vue实例的属性：

    `new Vue(对象)`

    1. `el`字符串，选择器
    2. `data`对象，数据
    3. 生命周期钩子

        1. `created`方法
        2. `updated`方法
        3. `destroyed`方法
        4. 等
    4. `methods`对象
    5. `computed`对象，自动依赖而计算

        默认是`getter`，也可以设置`getter`+`setter`。
    6. `watch`对象，被watch的属性改变而执行
    7. `filters`对象，过滤器
    8. `components`对象，局部注册组件
6. 组件

    >所有组件都是被扩展的Vue实例：`new (Vue.extend({对象}))()`。

    1. 注册组件方式：

        >1. 组件名W3C规则：小写，且包含一个`-`。
        >2. 要确保在初始化Vue实例之前注册了组件。

        1. 全局

            `Vue.component('组件元素名', 对象)`
        2. 局部

            ```
            new Vue({
                components: {
                    '组件元素名':对象
                }
            })
            ```
    2. 使用组件：

        1. `<组件名></组件名>`

            >不会出错：`<script type="text/x-template">`、JS字符串模版、`.vue`组件。
        2. `<标签 is="组件名"></标签>`

    - 父子组件间的通信

        引用组件时都是父级作用域，组件有自己独立的作用域（父子组件间、Vue实例间作用域独立，子组件可以访问父级），只能通过`props`传递数据。

        1. 父->子，通过`props`向下传递初始化数据；不通过`props`的也能传入子组件，直接添加在DOM上，不传入组件或Vue实例。

            单向数据流

            1. `props`是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。
            2. 不应该在子组件内部改变`props`，定义一个局部变量（`data`），用`props`去初始化并接受子组件的数据变动。

            >注意避免**引用数据类型**导致的子组件改变父级。
        2. 子->父，通过`$emit`向上传递事件、参数

            1. 在父级使用子组件地方添加`v-on:自定义事件1="父方法"`监听。
            2. 在子组件添加`this.$emit('自定义事件1', 值)`向上传递。

    1. `template`字符串，字符串模板

        `<slot>`的使用，插入父级引入组件内部的内容。
    2. `props`

        1. 数组，创造新属性，接受父级传递内容
        2. 对象，新属性：数据类型

            数据类型：字符串/数组/对象（type、required、default、validator）
    3. `data`方法（return数据对象），`v-for`循环的每个实例都调用创建一份