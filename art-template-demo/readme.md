### 前端模版框架
- [artTemplate](http://www.jq22.com/jquery-info1097) 腾讯开源 [文档](https://aui.github.io/art-template/zh-cn/docs/index.html#%E7%89%B9%E6%80%A7)
- Mustache
- Handlebars
- doT
- Jude
- swig

### artTemplate特性
- 性能卓越，执行速度通常是 Mustache 与 tmpl 的 20 多倍  [性能测试](https://aui.github.io/art-template/rendering-test/)

- 支持运行时调试，可精确定位异常模板所在语句（演示）

- 对 NodeJS Express 友好支持

- 安全，默认对输出进行转义、在沙箱中运行编译后的代码（Node版本可以安全执行用户上传的模板）

- 支持include语句

- 可在浏览器端实现按路径加载模板（详情）

- 支持预编译，可将模板转换成为非常精简的 js 文件

- 模板语句简洁，无需前缀引用数据，有简洁版本与原生语法版本可选

- 支持所有流行的浏览器

## artTemplate语法
标准语法  {{ xxx }}
原始语法  <% xxx %>


### 主要方法

1 - 基于模板名渲染模板 template(filename, data);
```
// html
<div id="content"></div>
```
```
// js
<script src="template-web.js"></script>
<script id="tpl-user" type="text/html">
{{if user}}
  <h2>{{user.name}}</h2>
{{/if}}
</script>

<script>
var data = {
    list: [aaa, bbb, ccc],
    user: {name: 'Anita', age: 18}
};
var html = template('tpl-user', data);
$('#content').html(html);
</script>
```


2-将模板源代码编译成函数
```
template.compile(source, options);
```

3 - 将模板源代码编译成函数并立刻执行
```
template.render(source, data, options);
```

### 使用
1.npm
```
npm i art-template --save-dev
```
2.浏览器中编译
[template-web.js](https://unpkg.com/art-template@4.13.2/lib/template-web.js)