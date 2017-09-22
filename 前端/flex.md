> 为解决布局而生的css3新特性，非常灵活的布局模式，解决各种尺寸，浮动，块控制属性

1. Every child of a Flex Container is a Flex Item

## Container 属性

1. **flext-direction**  
   控制item排列方式，如：`row`, `row-reverse`,`column`,`column-reverse`，结合`writing-mode`显示排列方式

2. **justify-content**  
   item 横向上伸展方式
![](/assets/css/justify-content.png)
   * **flext-start**  默认
   * **flext-end **
   * **center** 居中
   * **space-between** 两边挤到边框，中间的元素等间距排列，适合靠壁排列
   * **space-around** 元素都等间距排列，margin差不多的距离，适合分散

3. **align-items** 对横向布局的补充，它可以纵轴坐标对其  
   ![](/assets/css/align-items.png)
   * **flext-start**
   * **flext-end** 向下对其
   * **center**  垂直居中
   * **baseline**  _不造为啥没变化_
   * **stretch** _为啥没扩展_

4. **flext-wrapper** 对行布局折断方案 
  * nowrap 即使定义了item的宽度，若不折断，那么items的宽度超过container了，会导致压缩item的width以适应容器
  * wrap 超过了容器就会另起一排
  * wrap-reverse 反转
  
5. **align-content** 他的效果和align-items差不多，但是它是配合flext-wrap以前使用的

6. **flext-flow** 它是对 flex-direction 和 flex-wrap 的综合使用

## Item 属性

1. **order** 改变item的排序方式，本来item默认是按文档结构的
2. **margin** 它可以用来控制浮动，排挤，居中，如: `margin:auto` 将垂直居中和水平居中
3. **align-self** 自对其方式，单独控制
  * stretch
  * flext-start、flext-end
  * center
  * baseline
4. **flext** 指定item占多少个比例，_重要属性_，如果不设置它的宽度就是里面内容的宽度

