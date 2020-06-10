# Less

> 中文网站

[Less中文网](https://less.bootcss.com/)

## 变量(Variables)

```less
@widtd: 10px;
@height: @width + 10px
  
    #header {
		width: @width;
        height: @height;
        }
```

> 编译后

```css
#header {
    width: 10px;
    height: 20px;
}
```

## 混合(Minins)

> 概念

混合是一种将一组属性从一个规则及包含（或混入）到另一个规则集的方法。

```css
.bordered {
    border-top: dotted 1px black;
    border-bottom: solid 2px black;
}
```

如果希望在其他规则集中使用这些属性，只需使用class名称即可

```less
#menu a {
    color: #111;
    .bordered();
}
.post a {
    color: red;
    .bordered();
}
```

## 嵌套（Nesting）

> 概念

代替css层叠或于层叠结合使用的能力

```css
#header {
    color: black;
}
#header .navigation {
    font-size: 12px;
}
#header .logo {
    width: 300px;
}
```

使用less后

```less
#header {
    color: black;
    .navigation {
        font-size: 12px;
    }
    .logo {
        width: 300px;
    }
}
```

可以将伪类选择器与混合一同使用（& 表示当前选择器的父级）

```less
.clearfix {
    display: block;
    zoom: 1;
    &:after {
        context: '';
        display: block;
        font-size: 0;
        height: 0;
        clear: both;
        visibility: hidden;
    }
}
```

> @ 规则嵌套和冒泡

@规则（例如@media 或 @supports） 可以与选择器以相同的方式进行嵌套。@ 规则集会被放在前面，同一规则集的其他元素相对顺序保持不变。这叫做冒泡（bubbling）

```css
.component {
    width: 300px;
    @media (min-width: 768px) {
        width: 600px;
        @media (min-resolution: 192dpi) {
            background-image: url(/img/retina2x.png);
        }
    }
    @media (min-width 1280px) {
        width: 800px;
    }
}
```

编译后

```css
.component {
    width: 300px;
}
@media (min-width: 768px)  {
    width: 600px;
}
@media (min-width: 768px) and (min-resolution: 192dpi) {
    .component {
        background-image: url(/img/retina2x.png);
    }
}
@media (min-width: 1280px)  {
    .component {
        width: 800px;
    }
}
```

## 运算（Operations）

算数运算符 + - * / 可以对任何数字、颜色或变量进行运算。可能的话，算数运算符在做加、减或比较之前会进行单位换算。计算的结果以最左侧操作数的单位类型为准。如果单位换算无效或失去意义，则忽略单位。

无效的单位换算例如：px 到 cm 或 rad 到 % 的转换。

```less
// 所有操作数被转换成相同的单位
@conversion-1: 5cm + 10mm; //结果是 6cm
@conversion-1: 2 - 3cm - 5mm; //结果是 -1.5cm

//不可能转换的
@incompatible-units: 2 + 5px - 3cm; //结果是 4px

//变量示例
@base: 5%;
@filler: @base * 2; //结果是 10%
@other: @base + @filler //结果是 15%
```

乘法和除法不作转换。因为这两种运算符在大多数情况下都没有意义，一个长度乘以一个长度就得到一个区域，而css是不支持指定区域的。Less将按数字的原样进行操作，并将为计算结果指定明确的单位类型

```less
@base: 2cm * 3mm //结果是 6cm
```

你还可以对颜色进行算术运算

```less
@color: #224488 / 2; //结果是#112244
background-color: #112244 + #111; //结果是 #223355
```

不过，Less 提供的 [色彩函数](https://less.bootcss.com/functions/#color-operations) 更有使用价值。

> calc() 特例

为了与 CSS 保持兼容，`calc()` 并不对数学表达式进行计算，但是在嵌套函数中会计算变量和数学公式的值。

```less
@var: 50vh / 2;
width: calc(50% + (@var - 20px)) //结果是 calc(50% + (25vh -20px))
```

