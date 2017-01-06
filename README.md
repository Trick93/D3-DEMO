# D3-DEMO
学习DS.JS过程中一些demo
# 实例中D3.JS常用的API

标签（空格分隔）： 未分类

---
## 选择API ##
以下的**selection**就是.selectAll()返回的集合

 1. selection.data()([values[,key]]):连接指定的一组数据和当前选择。指定的values是一组数据值或一个函数返回一组值。

    data操作的结果是update选择；这表示选择的DOM元素已经成功绑定到指定的数据元素。update选择还包含对enter和exit选择，对应于添加和删除数据节点。
    

 2. selection.enter():返回输入选择-当前选择中存在但是当前dom元素中还不存在每个数据元素的占位符节点。此方法只在由data运算符返回的更新选择中定义。我们希望创建新的节点来匹配我们的数据，简单例子：
```
d3.select("body").selectAll("div")
    .data([4,8,15,16,23,42])
    .enter().append("div")
    .text(function(d) {return d;});
```
假如body最初是空，上面代码将创建六个新的div元素，将按顺序追加到body，并指定文本
```
    <div>4</div>
    <div>8</div>
    <div>15</div>
    <div>16</div>
    <div>23</div>
    <div>42</div>
```

    
 3. selection.exit()：返回退出选择-找出当前选择存在的DOM元素中没有新的数据元素，因此一般使用的话是在用data运算符返回的更新选择时候。注意！exit操作符只是返回一个exit选择引用，需要自己删除节点。一个简单的例子，考虑更新在上面例子中的enter操作符创建六个DIV元素，在这里，我们把这些元素和一个含有一些新的，一些旧数据的新数组绑定：
```
    var div = d3.select("body").selectAll("div")
        .data([1,2,4,8,16,32], function(d) {return d;});
```
现在的div-data操作符的结果--指的是更新选择。因为我们指定的key函数使用标识函数，并且将新数据数组包含数字【4,8,16】，它也存在旧数据中，因此这个**更新**包括三个DIV元素【1,2,32】，可以实例化并添加进去
```
    div.enter().append("div").text(function(d) {return d;});
```
同时可以删除退出的元素【15,23,42】，div.exit().remove(),现在如下：
```
    <div>4</div>
    <div>8</div>
    <div>16</div>
    <div>1</div>
    <div>2</div>
    <div>32</div>
```

## 树布局API ##

 **1、 d3.layout.tree - 整齐地排列树节点**  
创建一个新的树布局，使用默认的设置，想要自己定义设置可以使用后面的API接口定义

 **2、 tree.nodes() - 计算父布局并返回一组节点**
***tree.nodes(root)***返回树的当前节点集合，通过操作这个集合，可以实现节点的动态变化。树布局遵循相同的基本结构：布局的输入参数是层次结构的根节点，输出返回值是表示所有节点的计算位置的数组。在每个节点上填充几个属性：
    * parent-父节点，或者为null
    * children-子节点数组，或叶节点为null
    * depth-节点的深度，从根开始为0
    * x-节点位置的计算x坐标
    * y-节点位置的计算y坐标
一般取得节点集合可用如下代码：var nodes = tree.nodes(root).reverse()

 **3、 tree.links(nodes) - 计算树节点的父-子连接**
给指定的节点数组，例如由节点返回的节点数组，返回表示每个节点从父节点到子节点的链接的对象数组。叶节点将不具有任何链接。每个链接都是具有两个属性的对象
    *source-父节点
    *target-子节点
这个方法通常结合对角线生成器，一般代码如下：
```
    svg.selectAll("path.link")
        .data(tree.links(nodes))
        .enter().insert/append("path","g")
        .attr("d",d3.svg.diagonal())
```
 **4、 tree.children - 取得或设置孩子访问器**
 **5、 tree.separation - 取得或设置相邻节点的间隔函数**
如果指定了分隔，则使用指定的函数来计算相邻节点之间的间隔，如果未指定分隔，则返回当前分隔函数，默认为：
```
    function separation(a,b){
        return a.parent == b.parent ? 1 : 2;
    }
```
```
    function separation(a,b){
        return (a.parent == b.parent ? 1:2) / a.depth;
    }
```
分离函数通过两个相邻节点a和b，并且必须返回节点之间的期望间隔。节点通常是同胞，但是如果布局决定将这一的节点放置在邻近，则节点也可以是同胞
**6、 tree.size - 用x和y指定树的尺寸**
如果指定size，则将可用布局大小设置为指定的表示x和y的二元数组数组。 如果未指定size，则返回当前大小，默认为1×1。 布局大小在x和y中指定，但是这不是受限的屏幕坐标，并且可以表示任意的坐标系。 例如，为了产生以度数测量树宽度（x）的径向布局，并且树深度（y）是以像素为单位的半径r，例如[360，r]。
