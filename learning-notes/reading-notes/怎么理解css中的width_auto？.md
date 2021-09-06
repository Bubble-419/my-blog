# 1. wdith属性修改的到底是谁的width？
## 1.1 盒尺寸与width属性
盒尺寸由外到内分别包括`border-box`,`padding-box`,`content-box`,（并不包括margin），默认来讲，当box-sizing没有被设置为`border-box`时，width设置的是content-box的宽度。

这也是当我们再给盒子设置padding和border时，盒子的表现宽度会被“撑大”的原因，就好像包子馅和包子皮之间的关系。

另外需要注意的是，当我们直接设置了盒子的width，盒子的流动性就会丢失了。

![image](https://user-images.githubusercontent.com/58065379/115661259-bfd47000-a36f-11eb-9513-bb71d0ecad5a.png)

## 1.2 宽度分离原则
为了解决上述两个问题，我们可以采取宽度分离的原则，即把width和padding、border的定义分离开。

把width赋给父盒子，让子盒子利用流动性填满父盒子，此时再设置padding和border，也不会导致视觉宽度受影响。（前提是没有设置浮动、绝对定位等等，否则width会默认为内容宽度）



# 2. 不设置width属性值（默认为auto）时，元素的width是如何界定的？
1. **充分利用可利用空间**

  > 例：当网页中只有一个定义了height的div盒子，它的宽度会默认为整个页面。

  这很符合css的流动性，盒子像水一样自动填满了整个空间。

2. **根据元素大小收缩至合适宽度**
     浮动、绝对定位、行内块、table元素都能典型地说明width的`shrink-to-fit`特性，会根据内容的大小确认宽度。

3. **最小宽度**
     常见于表格中，由于宽度不够，一列被强行挤开成好多行。

4. **超出父盒子宽度**
     一般来说元素的宽度不会超出父盒子，如果定义了white-space属性为nowrap，则会宽度自动超出。

# 3. box-sizing属性对width有什么影响？
上文所述，width默认设置的是content-box的宽度，而当box-sizing出现后，css允许我们把width作用在`border-box`身上，则`box-sizing: border-box;`翻译给浏览器听就是：

*我声明的宽度包括了 border 、padding 和 content 。*

## 3.1 再理解box-sizing
不得不说box-sizing是个很好用的属性，mdn也说明了“当使用`box-sizing: border-box;`来布局时，很多问题都会迎刃而解”。

但是我们不得不深入思考的是，box-sizing真正指向点是什么？

这一点可以从默认样式为`box-sizing: border-box;`的元素中窥见一二：

> ** `box-sizing: border-box` is the default styling that browsers use for the `<table>`, `<select>`, and `<button>` elements, and for `<input> elements whose type is radio, checkbox, reset, button, submit, color, or search`.**

上述元素都是默认样式就自带了border和padding的，此时我们就不能按照常规方式把width值设置为它们的content-box，于是就有了`box-sizing`。

于是我们可以知道，box-sizing一开始其实是为了让我们更方便设计这些自带padding和border的元素，应运而生的。
这些元素也很容易让我们联想到替换元素(replaced elements)，常见的有audio, canvas, img, input, object, 和 video。把可替换元素设置为`box-sizing: border-box;`，可以更好地掌控它们的布局。
