# xs-select使用

```javascript
    var Select = xmSelect.render({
        el:'#tag',
        tips:'', //默认提示，类似placeholder
        name:'', //后台提交收到的name
        layVerify:false, //不能为空
        filterable: true, //是否开启搜索
        direction: 'down', //下拉框方向,up,down,auto
        max:3, //最多可选几个标签
        prop: { //自定义后台发来的动态数据属性
            name: '',
            value: ''
        },
        initValue:[], //注意这里是数字数组
        maxMethod(seles, item){   //选多了的提示
            layer.msg(`最多选择三个标签`);
        },
        theme: { //定义选多了选择框的颜色提示
            maxColor: 'red',
        },
        toolbar: {  //工具条,可自定义选择需要的工具:list: [ 'ALL', 'CLEAR', 'REVERSE' ]
            show: true,
            list:['CLEAR']
        },
        autoRow: true, //自动换行
        data:[]
    })

    //动态获取数据
    axios({
        method:'get',
        url:'',
    }).then(response => {
        const res = response.data; //注意这里使用const
        Select.update({
            data:res,
        })
    })
```

