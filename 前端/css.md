### inline-block

效果是不换行，我们以前习惯用float来做浮动响应式，但若碰到不浮动的块，还需 clear，若都使用 inline-block，那么不浮动块是无需clear的

### float

浮动，可以使用它来不换行，让块按浮动方向接在一起，若某一块元素不希望float，可以使用 clear: left\|both 实现。

### border-sizing

border-sizing: border-box 会让盒子的内边距与外边距都无效

### position ~ static

css元素的默认定位方式，正常定位方式

### position ~ relative

不脱离文档流，相对于前一个元素可偏移，若无偏移，这个定位不表现出什么效果

