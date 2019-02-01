## animation（动画）、transition（过渡）、transform（变形）、translate（移动） 
[点击]（https://www.cnblogs.com/shenfangfang/p/5713564.html）

### transform （用于内联元素和块级元素）
包含属性：旋转rotate、扭曲skew、缩放scale和移动translate以及矩阵变形matrix；


### animation
**animation** { animation-name  animation-duration  animatino-timing-function  animation-delay animation-iteration-count animation-direction animtion-play-state  animation-fill-mode}

- animation-name：用来调用@keyframes定义好的动画，与@keyframes定义的动画名称一致
- animation-duration ：指定元素播放动画所持续的时间
- animatino-timing-function ： 和transition中的transition-timing-function 中的值一样。根据上面@keframes中分析的animation中可能存在多个小动画，因此这里的值设置是针对每一个小动画所在时间范围内的属性变换速率。
- animation-delay：定义在浏览器开始执行动画之前等待的时间，这里是指整个animation执行之前的等待时间，而不是上面说的多个小动画
- animation-iteration-count：定义动画的播放次数，其通常为整数，默认是1,；取值为infinite，动画将无限次的播放。
- animation-direction：主要用来设置动画播放方向，其主要有两个值：
    normal 默认值，如果设置为normal时，动画每次循环都是向前（即按顺序）播放
    alternate（轮流），动画播放在第偶数次向前播放，第奇数次向反方向播放（animation-iteration-count取值大于1时设置有效）
- animtion-play-state：属性是用来控制元素动画的播放状态。其主要有两个值：
    running，可以通过该值将暂停的动画重新播放，这里的重新播放不是从元素动画的开始播放，而是从暂停的那个位置开始播放。
    paused，暂停播放
    注意：使用animtion-play-state属性，当元素动画结束后，元素的样式将回到最原始设置状态（这也是为什么要引入animation-fill-mode属性的原因）
- animation-fill-mod：　默认情况下，动画结束后，元素的样式将回到起始状态，animation-fill-mode属性可以控制动画结束后元素的样式。主要具有四个属性值：
    none（默认，回到动画没开始时的状态。）
    forwards（动画结束后动画停留在结束状态）
    backwords（动画回到第一帧的状态）
    both（根据animation-direction轮流应用forwards和backwards规则）


    
　animation（动画）、transition（过渡）是css3中的两种动画属性。animation强调流程与控制，对元素的一个或多个属性的变化进行控制，可以有多个关键帧（animation 和@ keyframes结合使用）；

　　transition强调过渡，是元素的一个或多个属性发生变化时产生的过渡效果，同一个元素通过两个不同的途径获取样式，而第二个途径当某种改变发生（例如hover）时才能获取样式，这样就会产生过渡动

画。可以认为它有两个关键帧（Transition ＋ Transform ＝ 两个关键帧的Animation）。

### transition
transition{ transition-property  transition-duration  transition-timing-function  transition-delay}
transition:{none/all/indent xxx(s) ease（逐渐变慢）/linear（匀速）/ease-in(加速) /ease-out（减速）/ease-in-out（加速然后减速）/ cubic-bezier（允许自定义一个时间曲线）}

不能产生过渡效果：
- background-image，如url(a.jpg)到url(b.jpg)（与浏览器支持相关，有的浏览器不支持）等，浏览器支持情况
- float浮动元素
- height或width使用auto值
- display属性在none和其他值（block、inline-block、inline）之间变换
- position在stativ和absolute之间变换

