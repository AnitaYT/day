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