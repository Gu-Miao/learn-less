# 概览

## 变量

非常简单，直接看代码：

```less
// 声明变量
@width: 10px;
// 变量声明中可以使用其他变量
@height: @width + 10px;

#header {
  // 使用变量
  width: @width;
  height: @height;
}
```

CSS 输出：

```css
#header {
  width: 10px;
  height: 20px;
}
```

参考：

- [Less 官网·概览·变量](https://lesscss.org/#variables)

## 混合(Mixin)

混合是 Less 中一种方法，什么方法呢？将**一个规则集混入到另一个规则集中的方法**。

```less
// 定义一个类选择器(规则集)
.bordered {
  border: 1px solid red;
  border-radius: 6px;
  outline: none;
}

.link {
  color: blue;
  // 将 .bordered 规则集混入
  .bordered();
}
```

`.link` 的 CSS 输出：

```css
.link {
  color: blue;
  border: 1px solid red;
  border-radius: 6px;
  outline: none;
}
```

这种方式对提高代码的复用性有很大的帮助。

参考：

- [Less 官网·概览·混合](https://lesscss.org/#mixins)

## 嵌套

有时在 CSS 中为了避免样式冲突，我们会使用“再套一层”的方法：

```css
.container .header {
  /* ... */
}
.container .body {
  /* ... */
}
.container .footer {
  /* ... */
}
```

而 Less 提供了可嵌套的特性，因此我们可以这样写：

```less
.container {
  .header {
    // ...
  }
  .body {
    // ...
  }
  .footer {
    // ...
  }
}
```

这样不仅更加简便，而且还能清楚地展示元素之间的结构。

有些朋友可能会问，那伪类的样式要怎么写呢？其实很简单，我们举个例子：

```less
.container {
  // .container 元素的 before 伪元素
  &::before {
    // ...
  }
  // 鼠标滑过 .container 时的样式
  &:hover {
    // ...
  }
}
```

上面示例代码中的 `&` 称为**父级选择器**，它代表当前选择器的父级，也就是 `.container`，上面的示例转换成 CSS 后会输出为：

```css
.container::before {
  /* ... */
}
.container:hover {
  /* ... */
}
```

参考：

- [Less 官网·概览·嵌套](https://lesscss.org/#nesting)

## @规则嵌套与冒泡

@规则(At-rules)，比如常见的媒体查询 `@media`，判断 CSS 特性是否支持的 `@supports` 等在 Less 中都**可以像选择器一样进行嵌套**。我们先来看个例子：

```less
.component {
  width: 300px;
  @media (min-width: 768px) {
    width: 600px;
    @media (max-width: 1000px) {
      background: red;
    }
  }
  @media (min-width: 1280px) {
    width: 800px;
  }
}
```

CSS 输出：

```css
.component {
  width: 300px;
}
@media (min-width: 768px) {
  .component {
    width: 600px;
  }
}
@media (min-width: 768px) and (max-width: 1000px) {
  .component {
    background: red;
  }
}
@media (min-width: 1280px) {
  .component {
    width: 800px;
  }
}
```

可以看到在输出后的 CSS 中，**@规则至于外层**，即 `@mdia` 规则被置于 `.component` 的外层，**并且规则集内的所有元素相对位置保持不变**，这被称为**冒泡**。

参考：

- [CSS 机制@At-rule 详解](https://blog.csdn.net/a1056244734/article/details/114581201)
- [MDN 官网 @规则 ](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule)
- [Less 官网·概览·@规则嵌套与冒泡](https://lesscss.org/#nesting-nested-at-rules-and-bubbling)

## 运算

Less 中的加减乘除运算可以适用于任何数字、颜色或者变量。

Less 在做加减法或比较时会考虑单位，并且在运算前先进行换算，其结果会取用**最左侧明确声明的单位**：

```less
@conversion-1: 5cm + 10mm; // 最左侧明确声明的单位是 cm，因此将 10mm 转为 1cm，结果为 6cm
@conversion-2: 2 - 3cm - 5mm; // 最左侧明确声明的单位是 cm，因此 2 转为 2cm，5mm 转为 0.5mm，结果为 -1.5cm
```

但如果部分运算的单位转换无法进行，比如像素转为厘米，那么运算将会忽略单位：

```less
@incompatible-units: 2 + 5px - 3cm; // 像素无法转换为厘米，因此直接进行数值运算，再取用最左侧明确声明的单位 px，结果为 4px
```

长度\*长度的数学意义为一块区域，但 CSS 并不支持区域相关的特性，因此**乘除法不会进行单位转换**。因而，Less 会**直接进行数值运算，单位的取用规则与加减法一致**：

```less
@base: 2cm * 5px; // 2 * 5 = 10，最左侧明确声明的单位为 cm，结果为 10 cm
```

注意，从 4.0 版本开始，如果想进行除法运算，**必须将算式写在括号中**，否则 Less 将**不会编译这**部分：

```less
.component {
  padding: 10px / 2;
}
```

CSS 输出：

```css
.component {
  padding: 10px / 2; /* 没有被编译，原封不动保留了下来 */
}
```

正确写法为：

```less
.component {
  padding: (10px / 2); // 使用括号包裹除法算式
}
```

颜色运算：

```less
@color-1: (#222222 / 2); // #111111
@color-2: #121212 + #333; // #454545
```

不过颜色运算不常用，一般会用 Less 内置的处理颜色的函数。

参考：

- [Less 官网·概览·运算](https://lesscss.org/#operations)

## `calc()` 特例

为了与 CSS 兼容，`calc()` 函数不会去计算数学表达式，但会计算嵌套其中的变量：

```less
@var: 50vh * 2;
.component {
  width: calc(@var - (20px * 2)); // calc(100vh - (20px * 2))
}
```
