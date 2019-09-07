#### svg和canvas区别
svg:使用xml描述2D图形的语言，canvas:使用javascript绘制2D图形

#### svg(Scalable Vector Graphics)
- 二维矢量图形的一种图形格式,使用 XML 格式定义2D图形
- 图像在放大或改变尺寸的情况下其图形质量不会有所损失
- 由于svg是xml文件，任何文本编辑器都可以对它进行编辑，而且更适合seo优化。
- svg文件比jpg格式要小很多
- 文件体积小，能够被大量的压缩
- 图片可无限放大而不失真(矢量图的基本特征)
- 在视网膜显示屏上效果极佳
- 能够实现互动和滤镜效果

#### 兼容性
IE9+及主流移动浏览器

#### 常用方法
`<svg xmlns=”” width=”” height=”” version =“”></svg>`
xmlns ：SVG 命名空间;width、height：svg的宽和高,version ：定义所使用的 SVG 版本

#### viewBox 视区盒子
`viewBox="x,y,width,height"  // x:左上角的横坐标、y:左上角的纵坐标、width:视口的宽度、y:视口的高度`

#### preserveAspectRatio
`preserveAspectRatio="xMidYMid meet"`
作用对象： viewBox
两个部分组成
第一个值表示：viewBox如何与SVG viewport 对齐；第二个值表示如何维持宽高比（如果有的话）。其中第一个值又是由两个部分组成。前半部分表示x方向对齐，后半部分表示y方向对齐。
第二个值： meet/slice/none

### svg内标签



