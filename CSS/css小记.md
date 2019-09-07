### css基本语句构成
选择器，以及一条或者多条声明
每条声明由一个属性和一个值组成，属性和值用冒号分开
```
h1 {
    color: red;
    font-size: 16px;
}
```
### 自定义属性选中标签
```
html
    <p data-name="text">用data-*选中标签设置样式</p>
    <p>bbbb</p>
css:
    p[data-name="text"] {
        color: green;
    }
```

### css的属性position
[position](https://segmentfault.com/a/1190000011358507)


#### 页面解析过程  浏览器渲染过程
#### 浏览器渲染过程
- 1.解析Dom,生成Dom树; 解析Css,生成CSSOM树
- 2.Dom树和CSSOM树结合,生成渲染树（Render Tree）


### 重绘和回流 
回流： 计算渲染树在设备视口（viewport）内的确切大小和位置, 这个计算的阶段就是回流。
重绘： 通过渲染树和回流，知了可见节点的样式和几何信息（位置，大小),将渲染树上的节点转变为实际像素，这个阶段叫做重绘。

### 何时发生回流
- 添加删除可见dom元素
- 元素位置发生变化
- 元素尺寸发生变化（margin、padding、border-width）
- 内容发生变化(文本变化或者图片变化)
- 页面一开始渲染的时候
- 浏览器尺寸发生变化

注意：回流一定重绘，重绘不一定回流。


### 让Dom脱离文档流的方式
元素脱离了文档流就不在渲染树里面
- 隐藏元素，应用修改，重新限制


### map 的数据
##### parseInt(string, radix) 将一个字符串string转化成radix进制的数，radix是介于2-36之间的数。

```
['1','7','11'].map(parseInt)  = [1 NaN, 3]  
// 一进制系统中，7这个数字是粗存在
= '1','7','11'].map((value, index, array)=> parseInt(value, index, array))
```

#### 