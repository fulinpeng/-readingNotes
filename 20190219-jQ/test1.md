* http://www.cnblogs.com/aaronjs/p/3279314.html

1. 匿名函数自执行
    * 匿名函数自执行形成局部作用域，防止变量的全局污染
    * 将`window`和`undefined`以参数形式传入自执行函数，减少变量查找的时间
    * 如何能够访问到匿名函数自执行中的方法呢？
        * 将方法或属性挂载到window(全局对象)上

2. 为什么`$()`的返回结果是对象？
    ```js
    return new jQuery.fn.init( selector, context, rootjQuery );
    ```

3. 插件接口：extend
    ```js
    jQuery.extend = jQuery.fn.extend = function() {...}
    ```
    * 这个是连等，也就是2个指向同一个函数，怎么会实现不同的功能呢？这就是this 力量了
    * jQuery.extend 对jQuery本身的属性和方法进行了扩展
    * jQuery.fn.extend 对jQuery.fn的属性和方法进行了扩展

4. 静态方法和实例方法在jQuery中的关系

5. Callbacks 回调对象 : 函数的统一管理[类似发布订阅者模式]

6. 因为是引用传递所以不需要担心这个循环引用的性能问题(没懂)
    * `jQuery.fn.init.prototype = jQuery.fn;`这里没有循环引用
    * 什么时候回造成循环引用？？？
        * 引用数最少是1，导致占用的内存永远不会被回收，而且与作用域也有关系

7. jQuery查询的的对象是dom元素，查询到目标元素后，如何存储？

* 添加事件
    * jQuery.each 遍历合并15种事件统一增加到jQuery.fn上
    * arguments.length调用`this.on`、`this.trigger`

* jQuery.data() 与 jQuery.fn.data() 方法有区别，一个设置了会被覆盖，一个不会
    ```js
        var jq1 = $("body");
        var jq2 = $("body");

        jq1.data('a', 1);
        jq2.data('a', 2);

        jq1.data('a'); /*2*/
        jq2.data('a'); /*2*/
        // 数据被覆盖

        $.data(jq1, 'b', 3);
        $.data(jq2, 'b', 4);

        $.data(jq1, 'b'); /*3*/
        $.data(jq2, 'b'); /*4*/
        // 不会被覆盖
    ```

8. 还学到了什么？
    1. 处理"",null,undefined,false,返回this ，增加程序的健壮性
    ```js
    if ( !selector ) {
        return this;
    }
    ```
    2. 检测是否是标签
    ```js
    if (selector.charAt(0) === "<" && selector.charAt(selector.length - 1) === ">" && selector.length >= 3) {...}
    ```
    2. document.createDocumentFragment()是相当好用的，可以减少对dom的操作
    3. 为什么排版引擎解析 CSS 选择器时一定要从右往左解析？
        * 一个元素可能有若干子元素，如果每一个都去判断一下显然性能太差。而一个子元素只有一个父元素，所以找起来非常方便
        * 匹配的情况远远低于不匹配的情况，所以逆向匹配带来的优势是巨大的
    4. 如何使用选择器
        * 效率：id > tagName > className
        * 复合选择器代替Class选择器：`p.tit` 代替 `.tit`
        * 修饰ID选择器是画蛇添足：`#main #container` `div#main` ，非常慢，不要使用
        * 多用父子关系，少用嵌套关系：`.parent>.child` 代替 `.parent .child`
    5. jQuery复杂在做兼容、容错、函数重载

9. jQ中那些技巧：
    1. jQ就是：选择dom，操作dom，jQ对外提供一些工具方法
    2. jQ获取dom后，实例是一个类数组，每个元素都放在jQ实例对象上的
    3. 合并对象
        * Object.assign(target, obj, ...) 可以合并数组也可以合并对象
        * {...a, ...b}
    4. jQ中 forEach 方法，的回调的 this 指的是：每次遍历的结果值，普通类型就是它的包装对象
    5. $('div').each(callback);  可以遍历该jQ伪数组，怎么做到的
        * 用extend给fn添加了一个each方法，该方法里面用call方式调用了jQuery.foreach(this, callback)
    6. 设置单个样式，和设置多个样式，用的是同一个函数，这么做的？还是多次调用了那个jQuery.css方法
        * 这个css方法可以好好学习下，除了了上面说的“多个”“单个“以外，还有return 也是一个亮点
    7. 还有$('div').each() 与 $.each()好好看看，什么区别
    8. $(this) 把dom转成jQ对象
    9. $this[$this.css('display') == 'none' ? 'show' : 'hide']() 这种写法怎么就优化了嘛，只能优化代码行数而已
    10. 利用钩子处理兼容与扩展的好处
        * 适配器这种模式对于扩展新功能非常有利
        * 如果采用钩子处理的话，我们就省去了一大堆if else的分支判断 `hooks = jQuery.attrHooks[name]`
        * 由于JS用对象做为表进行查找是比if条句与switch语句快很多
