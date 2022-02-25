# 原由 & 前言

- 如果你没有听说过[tailwindcss](https://www.tailwindcss.cn/)，请先`return`，本篇不谈论`tailwindcss`是什么怎么用，而是怎么优雅的使用。
- 如果你听说过，请继续阅读并考虑使用`tailwindcss`，因为看完之后：
  - 开发上：可能为你甚至你们的前端团队**节省很多写样式的时间**，也能让你或者你们的项目**开发体验有很大提升**
  - 生产上：你们的项目打出来的包体积中的**样式代码占比将突然骤降然后趋于不变**。

# 你真的需要 css 预处理器吗

可能大多数同学在开发或者维护一个项目的时候，项目中应该都使用了诸如`scss`、`less`、`stylus`...等`css`预处理语言，甚至有的单个项目中使用了多种预处理语言，可能是为了使用**变量**，可能是为了**复用样式**方便，也可能是为了写一些**函数**方便我们对一些样式值做一些处理，但是大部分时候我们是为了**可以写嵌套样式**，总之是为了提高我们的开发效率，这一切在`tailwindcss`出现之前都是那么的美好，直到我们遇到了`tailwindcss`，你会发现你甚至连`stylelint`都不需要配置，因为你可能根本不用写`css`

# How & Which Version

## tailwindcss v2 vs v3

如果你的项目需要兼容`IE`，请使用`v2`版本，如果不需要请果断上`v3`版本

具体如何在你的项目中使用`tailwindcss`，请务必查看官网文档[v2 中文](https://www.tailwindcss.cn/docs/installation) [v3English](https://tailwindcss.com/docs/installation)，推荐基于`postcss`来添加

## 安装 vscode 插件

如果你决定使用`tailwindcss`，安装`bradlc.vscode-tailwindcss`这个官方提供的插件，可以提供提示、补全、查看实际设置的样式的能力

# 配置你的 tailwind

如果你已经按照文档教程创建了`tailwind.config.js`文件，那么接下来我们要对这个文件进行进一步的配置

## PC 端项目

如果你的项目是只针对`PC`端网页的，可能你只需要问你们的设计同学你们项目中的一些**基础的设计原则**有哪些，比如会使用的一些**主题颜色**，**字体大小、梯度**、**内外边距大小、梯度**，**常用的边框圆角大小、梯度**，**边框宽度、梯度**，如果设计同学没有这些原则，就协商一下这些基本原则，比如都会用到哪些颜色啊，边距单位一般按照`4px`的倍数来设置啊……

### 颜色

如果设计同学提供了项目中的主题色，并且有语义化的名称，比如类似`success`，`info`，`warning`这种语义化的颜色，我们就可以基于这些来配置我们的颜色，包括但不限于字体、背景、边框、阴影颜色(配置完之后直接可以使用类似`text-success`的类来设置颜色)，可以替换`css`预处理器的变量功能

```javascript
// tailwindcss v3
const colors = {
  'success': '#654321'，
  'info': '#123456',
  'warning': '#666666',
  // ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    colors,
  },
  plugins: [],
}
// tailwindcss v2
const colors = {
  'success': '#654321'，
  'info': '#123456',
  'warning': '#666666',
  // ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    textColor: colors,
    backgroundColor: colors,
    borderColor: colors,
  },
  plugins: [],
}
```

`v2`和`v3`版本的设置有些许区别，`v2`具体的设置什么类别的颜色需要特别指定。

### 间距 & 宽高 & 行高 & 圆角 & 边框宽度

因为`tailwindcss`默认长度相关的配置是基于`rem`的，而`PC`端的项目大多数时候我们都是固定一个宽度，左右留白，所以大多数情况下，设计稿都会在固定一个宽度内，元素的大小宽高边距单位都是`px`，所以我们需要对默认的做一些特定配置来适配我们的项目

```javascript
const spacing = {
  0: 0,
  4: "4px",
  8: "8px",
  12: "12px",
  // ... 项目中常用的都可以配置
};
module.exports = {
  content: ["./index.html", "./src/**/*.{vue,js,ts,jsx,tsx}"],
  theme: {
    // v3 & v2
    spacing,
    lineHeight: spacing,
    borderWidth: spacing,
    borderRadius: spacing,
  },
  plugins: [],
};
```

# 移动端适配方案

可能对于移动端适配，现在流行的就是`viewport`方案了，也可能有的项目还在用`flexable`方案，但是我们又不想手动换算`px`到`rem`或`vw`，虽然社区也有类似`pxtorem`或者`pxtovw`这种`postcss`的插件，但解决问题的方法还是不够优雅，可能是因为插件的维护的不积极，可能是插件不好用，不兼容`postcss8`(`pxtovw`说的就是你 😤)，既然我们都有`tailwindcss`了，那就让这些配置变的更简单一些吧！如果你们设计同学提供了常用的间距方案，比如`4px`的倍数或者`6px`的倍数，现在假设设计同学的设计稿都是`750px`的，我们就可以基于此来写两个函数方法来处理`pxtorem`和`pxtovw`的任务，如果你们是`flexable`方案就用`pxToRem`，如果是`viewport`的适配方案就用`pxToVmin`。

```typescript
function pxToRem(variable) {
  return `${variable / 75}rem`
}

function pxToVmin(variable) {
  return `${variable / 7.5}vmin`
}
// flexable
const fontSize = {
  12: pxToRem(12),
  14: pxToRem(14),
  16: pxToRem(16),
  ...
}, spacing = {
  0: 0,
  4: pxToRem(4),
  8: pxToRem(8),
  12: pxToRem(12),
  ...
}
// viewport
const fontSize = {
  12: pxToVmin(12),
  14: pxToVmin(14),
  16: pxToVmin(16),
  ...
}, spacing = {
  0: 0,
  4: pxToVmin(4),
  8: pxToVmin(8),
  12: pxToVmin(12),
  ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    fontSize,
    spacing
  },
  plugins: [],
}
```

当然圆角大小，边框宽度等都可以这么配置，是不是比使用插件优雅多了

# 嵌套语法

有的同学可能对`css`预处理器的嵌套语法情有独钟，`tailwindcss`里面可以吗？安排！
`tailwindcss`有一个插件叫[@tailwindcss/nesting](https://www.npmjs.com/package/@tailwindcss/nesting)，基于官网的一些[配置](https://tailwindcss.com/docs/using-with-preprocessors#nesting)，需要注意的是文档上的是给`postcss`配置`tailwindcss/nesting`，实际上需要配置`@tailwindcss/nesting`，然后就可以基于`css`提供嵌套的能力，试试呗~

# 固定行数后截断

有时候我们会为了写一个文本在`x`行之后阶段并显示`...`要写好几行样式代码，所以我们通常会定义一个这样的`scss`工具函数

```css
@mixin ellipsis($line: 1, $substract: 0) {
  @if $line==1 {
    white-space: nowrap;
    text-overflow: ellipsis;
  } @else {
    display: -webkit-box;
    -webkit-line-clamp: $line;
    -webkit-box-orient: vertical;
  }
  width: 100% - $substract;
  overflow: hidden;
}
```

`tailwindcss`对于这种特殊情况提供了专有的插件[@tailwindcss/line-clamp](https://www.npmjs.com/package/@tailwindcss/line-clamp)，只需要安装一下，然后在`tailwind.config.js`中的`plugins`中引入即可

安装插件

```bash
npm install -D @tailwindcss/line-clamp
```

配置

```javascript
// tailwind.config.js
module.exports = {
  // ...
  plugins: [require("@tailwindcss/line-clamp")],
};
```

使用

```html
<div class="line-clamp-3">
  <!-- ...3行后截断 -->
  <div></div>
</div>
```

# 多主题

可能你维护的是一个需要支持多主题的项目，不同的情况下有多种配色方案，在`tailwindcss`中配合`css var`来实现多主题配色会简单到让你窒息：

在你的全局`css`文件中配置主题，假设我们有`success`、`info`、`warning`这三种不同的主题配色

```css
/* global base css */
@tailwind base;
@tailwind components;
@tailwind utilities;
// 默认主题
:root {
  --success: 5 193 174;
  --info: 51 163 238;
  --warning: 237 214 18;
}
// 主题1的配色
.theme-1 {
  --success: 36 195 102;
  --info: 54 143 255;
  --warning: 234 209 27;
}
// 主题2的配色
.theme-2 {
  --success: 57 209 121;
  --info: 0 186 255;
  --warning: 234 209 27;
}
```

然后到我们的`tailwind.config.js`中改变我们的颜色配置

```javascript
// 让我们的颜色支持透明度设置
function withOpacityValue(variable) {
  return ({ opacityValue }) => {
    return opacityValue === undefined
      ? `rgb(var(${variable}))`
      : `rgb(var(${variable}) / ${opacityValue})`;
  };
}

const colors = {
  success: withOpacityValue("--success"),
  info: withOpacityValue("--info"),
  warning: withOpacityValue("--warning"),
  // ...
};
module.exports = {
  content: ["./index.html", "./src/**/*.{vue,js,ts,jsx,tsx}"],
  theme: {
    // ...
    colors,
  },
  plugins: [require("@tailwindcss/line-clamp")],
};
```

最后根据不同的情况设置你的主题，对要设置的主题的顶级元素设置对应的`class`即可，内部的所有颜色样式都会对应特定的主题而改变！

```html
<!-- 默认主题 -->
<div>
  <!-- ... -->
</div>
<!-- 主题1 -->
<div class="theme-1">
  <!-- ... -->
</div>
<!-- 主题2 -->
<div class="theme-2">
  <!-- ... -->
</div>
```

# 一些最佳实践

- 如果某些元素样式特别复杂，导致`html`代码很长很乱怎么优化？你可以通过`tailwindcss`提供的[@apply](https://tailwindcss.com/docs/functions-and-directives#apply)指令将一系列样式通过一个语义化的类表现出来

```html
<div class="complex-node">
  xxxx
  <div>
    // ...
    <style>
      .complex-node {
        @apply flex m-3 text-success rounded ....;
      }
    </style>
  </div>
</div>
```

- 我有一些样式是全局通用的，比如按钮，卡片的一些样式，我该怎么维护？你可以通过`tailwindcss`提供的[@layer](https://tailwindcss.com/docs/functions-and-directives#layer)指令将比较通用的样式`layer`到`components`层，作为组件级别的样式，从而可以达到全局复用的目的

```css
@layer components {
  .btn-blue {
    @apply bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded;
  }
}
```

- 我的项目是多人维护的，我如何保证原子化的样式类名称有一个比较合理的顺序呢？比如你喜欢先写宽高然后写定位，但是你的同事跟你相反，如何制定一个规范呢？`tailwindcss`提供了一个`prettier`插件[prettier-plugin-tailwindcss](https://www.npmjs.com/package/prettier-plugin-tailwindcss)，可以通过安装插件并且配置保存后更新即可规范不同成员写样式类的格式化问题

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

新建一个配置文件`.prettierrc.json`键入`{}`根据你们项目的原有配置进行配置，如果是个人项目也可以根据你个人喜好进行配置，然后在`vscode`中安装`esbenp.prettier-vscode`这个插件，然后打开你的设置搜索`format`，将`Format On Paste`和`Format On Save`都勾选上，就可以在保存完之后自动对你的样式类的顺序进行排序，排序的规则默认是根据`css box model`从外到内的规则进行排序的：

`margin` - `border` - `padding` - `content`

# 总结

只是针对平时笔者使用到的部分对`tailwindcss`的能力进行了一些浅谈，其实还有很多能力都没有说到，比如`v3`提供的一些针对打印页面的样式，`hover`、`active`、伪类等的一些设置，其实也比较简单，具体使用的时候看下官方文档就可以了。可能有部分小伙伴也会吐槽`tailwindcss`有很多基础类的名称需要记忆，其实也不多，熟能生巧，都是一劳永逸的东西。如果你也有不错的配置方案或者最佳实践也可以在评论区告诉我。最后，有用请点赞，喜欢请关注，我是`Senar`（公号同名），谢谢各位！

> 往期内容

[前端开发的基础生产力素养（后期不定期更新）](https://juejin.cn/post/7061152158132994061)

[记一次在老掉牙的 Vue2 项目中引入 TypeScript 和组合式 Api 和 vueuse 来改善大家伙的开发体验的艰辛历程](https://juejin.cn/post/7052529094738575374)

[各位 frontend developer 们，时机已经成熟，让我们开始用上 pnpm 吧](https://juejin.cn/post/7037690687063588877)

[一文让你彻底会用对象存储 OSS 的前端直传，不懂就再看一遍！（bushi）](https://juejin.cn/post/7032633964497993765)
