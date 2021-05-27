# Editor.md使用方法

Editor.md用来在网页上制作markdown编辑器，并能够将markdown文本转换为html文本。

1. 导入Editor.md

从Github或Gitee下载压缩包，解压，将解压后的文件夹放入项目中。

2. 在要使用的html文件中链接editormd.css和editormd.js文件。
3. 创建div 标签，设定id。
4. 在js文件中渲染。

## 具体例子

html文件：

```html
<div id="editor">
    <textarea id="my-editormd-markdown-doc" class="editormd-markdown-textarea"
              name="markdown" style="display:none;"></textarea>
</div>
```

js文件:

```javascript
$(function () {
    editormd("editor", {
        width   : "90%", //编辑器宽度
        height  : 800,  //编辑器高度
        syncScrolling : "single",
        path    : "/smart-api/htdocs/mdeditor/lib/", //editormd文件夹lib文件夹在项目中的路径，很重要，写不对不行
        saveHTMLToTextarea : true, //可以在第一个文本域下再写一个文本域元素，添加类为：editormd-html-textarea,这个										属性就会将转换的html放到第二个文本域中，但不显示

        emoji: true,//emoji表情，默认关闭
        taskList: true,
        tocm: true, // Using [TOCM]
        tex: true,// 开启科学公式TeX语言支持，默认关闭

        flowChart: true,//开启流程图支持，默认关闭
        sequenceDiagram: true,//开启时序/序列图支持，默认关闭,

        dialogLockScreen : false,//设置弹出层对话框不锁屏，全局通用，默认为true
        dialogShowMask : false,//设置弹出层对话框显示透明遮罩层，全局通用，默认为true
        dialogDraggable : false,//设置弹出层对话框不可拖动，全局通用，默认为true
        dialogMaskOpacity : 0.4, //设置透明遮罩层的透明度，全局通用，默认值为0.1
        dialogMaskBgColor : "#000",//设置透明遮罩层的背景颜色，全局通用，默认为#fff

        codeFold: true,

        imageUpload : true, //图片是否上传
        imageFormats : ["jpg", "jpeg", "gif", "png", "bmp", "webp"], //可上传的图片格式
        imageUploadURL : "/smart-api/upload/editormdPic/", //上传图片的保存路径

        /*上传图片成功后可以做一些自己的处理*/
        onload: function () {
           
        },

        /**设置主题颜色*/
        editorTheme: "pastel-on-dark", //编辑器主题
        theme: "gray",   //工具栏主题
        previewTheme: "dark"  //预览主题
	});
})	    
```





如果要修改已编辑的文章，使用`$("#textarea的ID").val("content")`，来设置文本域的内容。