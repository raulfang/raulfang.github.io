Vue组件之间的通信主要归为如下形式：
父子组件之间的通信
任意两个组件之间的通信（父子组件通信，爷孙组件通信，兄弟组件通信）
最终的boss，Vuex-状态管理模式
由繁到简，我们可以首先探讨一下父子之间的通信

子组件想改父组件的```data```
父组件传属性给子组件，子组件发事件给父组件，父组件再传属性给子组件，如此循环。
```
<template>
    <div class="parent">
        <p>父:给你{{ money }}元钱</p>
        <kid :money=" money" @repay="repay"></kid>
        <br>
        <button @click="add">那给你加100</button>
        <p v-if="back" @repay="repay">子：超过500我不要{{ back }}元</p>
    </div>
</template>
<script>
export default {
    name: 'parent',
    data () {
        return {
            money: 100,
            back: 0
        }
    },
    components:{ kid },
    methods:{
        repay (back) {
            this.back = back
        },
        add(){
            this.money += 100;
        }
}
```
爷孙组件间的通信
本质：两次父子间通信
情形一：爷爷控制孙子
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <script src="https://cdn.bootcss.com/vue/2.5.13/vue.min.js"></script>
  <script src="https://cdn.bootcss.com/vuex/3.0.1/vuex.min.js"></script>
  <script src="https://cdn.bootcss.com/vue-router/3.0.1/vue-router.min.js"></script>
</head>
<body>
  <div id="app">
    <p>{{ message }}</p>
    <button @click="xxx = true">打开</button>
    <hr>
    <child :visible="xxx" @close="log"></child>
  </div> 

</body>
</html> 
```
```
Vue.component('child', {
  props: ['visible'],
  template: `
    <div>
      我是儿子
      
      <grand-child v-show="visible" @close="$emit('close')"></grand-child>
    </div>
  `
})
Vue.component('grandChild', {
  template: `
    <div>
      我是孙子
      <button @click="$emit('close')">关闭</button>
    </div>
})
new Vue({
  el: '#app',
  data: {
    message: '我是爷爷',
    xxx: false,
  },
  methods: {
    log() {
      console.log('爷爷收到消息')
    }
  }
})
```
情形二：孙子向上一层一层通知爷爷
兄弟组件之间的通信
想要在两个兄弟组件之间通信，需要一个中间的桥梁，那么在这里我们可以使用 eventbus。

创建一个Vue对象
```
// bus.js
export default new Vue();
```
使用$on进行监听
```
import Bus from '../bus.js';
export default {
  ready() {
    Bus.$on('loadSuccess', text => {
      this.show = true;
      this.status = 'loadSuccess'
      if (text) {
        this.text = text;
      }
    })
  },
}
```
使用$emit触发事件
```
ajax({
  url: Config.API_ADDRESS_ADD,
  type: 'post',
  dataType: 'json',
  data: data
}).then((data)=>{
    Bus.$emit('loadSuccess', '添加成功！');
}).catch((data)=> {
})
```
使用$once注册，触发之后即被销毁。
使用$off解绑事件

以上就是Vue组件通信的几种形式，另外，当项目比较大，通信比较复杂时，我们可以考虑使用Vuex 。
