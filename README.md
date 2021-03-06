> 目前公司里有个展开收起的文本显示需求，这其实是一个非常普通的需求，蛮多的地方都会有用到，网上大多数的实现方式是都是依赖于Textview或者间接继承于Textview，比如首先有一段过长的文本内容，先给textview.settext（） 设置进去，然后给textview添加一个布局渲染监听，监听到以后通过textview.getLineCount()获取当前的总行数，然后再去截取。
>  </br>
> 这种方式其实多多少少有点问题，即使是一些封装比较好的逻辑中，始终可能会出现一种情：布局上先显示出了原本的内容，之后再修改为自己预期的内容。综合考虑下，打算自己写一个自定义view来实现，在onDraw层面就进行了控制操作

</br>
</br>

*实现大致逻辑：*

 1. *计算出当前文本在当前宽度下总共可能会占用几行*
 2. *如果占用行数超过预期，则在对应的位置显示 超出提示【...】,并且后面跟着俩个可提供  展开/收起 操作的按钮 *

具体效果如下：
![展示效果](https://img-blog.csdnimg.cn/20190719150255188.gif)

其实就是不断的计算宽度然后来计算是否匹配预期值，感觉是没什么好说的。这里实现的时候当时是遇到一个问题，如果是用canvas.getTextBounds(...)来计算文本的宽度内容然后进行累计的话，会有一个情况是一行的文本内容超出了预期值，感觉应该是计算的值不够精确的问题，后面参考了textview中进行绘制文本的方式，在textview中对文本的绘制都是用的 Layout 类进行渲染的，其中有一个`staticLayout` 类，可以测量出文本的行数以及对应行数位置下的行宽，主要就是使用该类进行对应的计算操作。


简单介绍下这个自定义view的一些功能

 - 支持 展开/收起  这俩个的文本自定义，可以设置为其他文本，同时可以设置这俩个状态下的距离左边值，以及在收起的状态下特殊按钮距离右边的值（主要是为了调整ui美观）
 - 支持设置文字行间的距离
 - 支持扩大 展开/收起 的点击热区范围控制（需要扩大多少自己设置，分别是纵向的扩大多少和横向的扩大多少）
 - 支持主内容和特殊内容的字体粗体，字体大小，字体颜色的分别设置
 - 支持最大行设置（具体判断也依据该字段为准）
 - 支持当达到最大行数时的前缀设置，就是 ...  这三个可以自定义设置为其他文本内容
 - 特殊字符的过滤，内部过滤了空格，换行和制表符的特殊字符
