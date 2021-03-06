
写代码时，具有明晰的、一致的代码风格，可以使代码更具可读性，以及降低维护成本，增加工作效率。在团队协作中尤其重要，每一个程序员都应该严格遵守既定的代码风格。这是职业素养很重要的一部分~

下面，是我根据已有的，稳定的代码风格文档总结的前端代码风格指南。

# 基本原则

1. 使用2空格缩进，不要使用 Tab 或者 Tab 空格混合；
2. 编码使用 UTF-8 方案；

# HTML规范

1. 语义化，使用 HTML5 提供的具有语义的标签；
2. 元素嵌套应该缩进；
3. 可选闭合标签必须闭合；
4. 无闭合标签末尾的斜杠最好不写；
5. 尽量简洁，减少标签数

# JS规范
1. 不要直接调用原生方法，例如 `object.hasOwnProperty(key)`，而是使用 `Object.prototype.hasOwnProperty.call(object, key)` 的方式，并且最好将所有的原生函数访问包装在单独的模块中，赋给简短易懂的变量，然后在需要的模块中通过 import 来引入调用
2. 通过扩展运算符来实现对象的浅复制，而不是使用 `Object.assign()` 方法
3. 同上，使用扩展运算符复制数组，而不是遍历的方法

# 参考资料

- [Idiomatic CSS](https://github.com/necolas/idiomatic-css)
- [Maintainablecss](http://maintainablecss.com/)
- [Code guide](http://codeguide.co/)
- [前端开发规范手册](http://zhibimo.com/read/Ashu/front-end-style-guide/index.html)
- [Google HTML/CSS 风格指南](https://google.github.io/styleguide/htmlcssguide.html)
- [Airbnb JS 规范](https://github.com/airbnb/javascript)
