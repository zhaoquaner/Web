# 2-Layui简单内容

## 布局



Layui有一套栅格系统，将容器进行十二等分。

栅格系统规则：

- 使用layui-row来定义行，如`<div class="layui-row"></div>`

- 使用`layui-col-md*`来定义一组列，放在行内。

    其中，md代表不同屏幕的标记，md就是电脑的中等桌面，正常用这个就行

    变量*代表该列所占用的12等分数，如果占3份，那么就是`layui-col-md3`

    如果多个列等分数的和刚好为12，那么就正好满行。超过12，多余的列就会另起一行。

    使用`layui-col-space*`：来定义列的间距，这个要放在有行`layui-row`的那个元素的class属性中。*代表间隔多少px。内置有1-20

    使用`layui-col-md-offset*`：来定义元素的偏移，例如`layui-col-md-offset3`，证明偏移3份，就是从第四等份开始显示元素。



## 颜色

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103202200625.png" alt="image-20210103202200625" style="zoom:67%;" />



还有其他七种内置颜色。已经介绍过了。



## 图标

通过使用一个内联元素，一般推荐i标签。设定`class="layui-icon"`，然后在加上想要用的图标的对应类。

比如`<i class="layui-icon layui-icon-loading">`就可以显示加载图标。

把图标当做文字，可以使用style关于文字的属性来设定图标的大小，颜色等属性。



具体图标和类对应关系去layui官网查。



## 动画

使用很简单，首先在想添加动画的元素的class属性添加`layui-anim`，然后再加上动画对应的class类名就可以了。

例如`div class="layui-anim layui-anim-up"></div>`

也可以添加多个动画。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103202944707.png" alt="image-20210103202944707" style="zoom:50%;" />



## 按钮

向任意的HTML元素设定`class="layui-btn"`，就可以建立一个默认按钮。

然后追加格式为`layui-btn-{type}`的class来定义其他风格。

可以自由组合，来形成多种按钮风格。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203802148.png" alt="image-20210103203802148" style="zoom:67%;" />![image-20210103203816682](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203816682.png)

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203802148.png" alt="image-20210103203802148" style="zoom:67%;" />![image-20210103203816682](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203816682.png)![image-20210103203842265](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203842265.png)

![image-20210103203842265](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203842265.png)



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103203900333.png" alt="image-20210103203900333" style="zoom:67%;" />





将按钮放入一个`class="layui-btn-group"`的元素中，可以形成按钮组。

按钮组，按钮之间间距很小，是挨着的。



将按钮放入一个`class="layui-btn-container"`的元素中，可以形成按钮容器。

按钮容器，按钮之间有一定间距。

