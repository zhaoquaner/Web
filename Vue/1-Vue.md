# 1-Vue

## 介绍

Vue是一套用于构建用户界面的渐进式框架，与其他大型框架不同的是，Vue被设计成可以自顶向上逐层应用。Vue的核心库只关心视图层，容易上手。还便于与第三方库或已有项目整合。另一方面，当于各种支持类库结合使用时，Vue也能够为负责单页应用提供驱动。



## 最简单的Vue应用

首先引入Vue.js文件。

创建html文件：

```html
<div id="demo">
    <h1>{{message}}</h1>
    <h1>{{getMessage()}}</h1>
</div>
<script>
	var demo = new Vue({
        el:#demo,
        data: {
        	message:'Hello Vue'
    	},
		methods: {
			getMessage:function() {
				return '第一个Vue项目'
			}
		}             
    })
</script>
```

每一个Vue应用都要实例化Vue。

上述例子中，实例化了一个Vue，并使用el属性绑定html元素，使用的是元素id。相当于一个Vue应用会挂载到一个DOM元素然后对该元素完全控制。HTML是入口，但其余都会发生在新创建的Vue实例内部。

其中：

- data用于定义属性
- methods用于定义函数，使用return来返回函数值
- `{{}}`来输出对象属性和函数返回值，直接使用定义的属性名和函数名即可

一个Vue实例只管指定id的元素，元素外的不受影响。

当一个Vue实例被创建时，它向Vue响应式系统加入了data对象中能找到的所有属性，当这些属性值发生改变时，html视图也会产生相应变化。



可以直接使用Vue实例调用data中的属性，例如：`demo.message`。

使用`$`来调用Vue实例的内置属性，用来和用户定义属性区别开。

例如要调用该Vue实例所有的data属性就用`demo.$data`，是一个对象，包含message属性。

