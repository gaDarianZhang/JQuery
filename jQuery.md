1. jQuery核心函数
       * 简称: jQuery函数($/jQuery)
       * jQuery库向外直接暴露的就是$/jQuery
       * 引入jQuery库后, 直接使用$即可
       * 当函数用: $(xxx)
       * 当对象用: $.xxx()

2. jQuery核心对象

    - 简称: jQuery对象
    - 得到jQuery对象: 执行jQuery函数返回的就是jQuery对象
    -  使用jQuery对象: $obj.xxx()

3. offset/position

    - offset: 元素相对于视口的位置
            给html加上Border，导致offset会比实际值少了这个border，也就是offset=实际值-html_border
    - position: 元素相对于最近的开启了定位的父元素的位置（父元素border内侧到子元素同侧的margin外侧的距离）
            如果元素的position是相对于视口的（也就是父元素都没开启position），那么position=实际值-2*html_border

4. scrollTop

    - scrollTop():读取/设置滚动条的Y坐标

        - $(document.body).scrollTop()+  (document.documentElement).scrollTop()
            读取页面滚动条的Y坐标(兼容chrome和IE)

        - $('body,html').scrollTop(60);
            滚动到指定位置(兼容chrome和IE)
            系统滚动条在IE和chrome内分别在body和html上加着的

            新版本的jQuery可以用document：$(document).scrollTop(200);

5. width()/innerWidth()/outerWidth()/outerWidth(true)\

    - 无论box-sizing是content-box还是border-box：
        - width() 内容区(content)宽度  可以用来设置，当box-sizing是border-box的时候width就是border-box，和后边三个不一样
        - innerWidth()  content+padding   能用来设置,但是如果box-sizing是border-box的话，结果就会有变化。设置inner-width其实是改变content的宽度，所以会拿给的innerWidth值减去padding，然后把这个结果赋给width。所以，如果是border-box的话，width就是content+padding+border了。
        - outerWidth()  content+padding+border  能用来设置，道理和innerWidth一样
        - outerWidth(true)  content+padding+border+margin   能用来设置，道理和innerWidth一样

6. 时间绑定与解绑

    - 事件绑定(2种)：on和off还有更复杂的参数，参考文档即可

        * eventName(function(){})
            绑定对应事件名的监听,    例如：$('#div').click(function(){});
        * on(eventName, funcion(){})
            通用的绑定事件监听, 例如：$('#div').on('click', function(){})
        * 优缺点:
            - eventName: 编码方便, 但只能加一个监听, 且有的事件监听不支持
            - on: 编码不方便, 可以同时给一个元素添加多个监听事件，如“click mouseenter”, 且更通用
            - <span style="color:skyblue">二者在绑定静态控件时没有区别，但是如果面对动态产生的控件，只有 on() 能成功的绑定到动态控件中。可以代替事件委托</span>
            - 同一种event绑定多个回调函数都不会覆盖
        * off([eventName[,functionName]]);
            - 不加参数则是释放这个元素上的所有事件
            - 加eventName则是释放该事件的所有回调函数
            - 加eventName和functionName则是释放元素上该事件的指定回调函数

    - ```javascript
        // 2. 给.inner绑定鼠标移入和移出的事件监听(用3种方法绑定)
        $(".inner")
            .on("mouseenter",function () {
                console.log("我是mouseenter");
            })
            .on("mouseleave",function () {
                console.log("我是mouseleave");
            });
        $(".inner")
            .on("mouseover",function () {
                console.log("我是mouseover");
            })
            .on("mouseout",function () {
                console.log("我是mouseout");
            });
        //hover()可以传入两个回调函数，第一个函数时移入的回调，第二个是移出的回调。
        // 如果只传入一个，那么移入移出都触发这一个函数
        $(".inner").hover(function () {
            console.log("我是hover移入");
        },function () {
            console.log("我是hover移出");
        });
        // 3. 点击btn1解除.inner上的所有事件监听
        $("#btn1").click(function () {
            console.log("解绑inner所有事件");
            $(".inner").off();
        });
        // 4. 点击btn2解除.inner上的mouseover事件
        $("#btn2").on("click",function () {
            console.log("解绑inner上的mouseover事件");
            $(".inner").off("mouseover");
        })
        ```

7. 事件委托

    - jQuery的事件委托API

        - 设置事件委托: $(parentSelector).delegate(childrenSelector, eventName, callback)
        - 移除事件委托: $(parentSelector).undelegate(eventName)

    - ```javascript
        // delegate事件委托
        $("ul").delegate("li","click",function () {
            this.style.backgroundColor = "hotpink";
        });
        $("#btn2").click(function () {
            $("ul").undelegate("click");
        });
        //也可以用on来实现事件委托
        $("ul").on("click","li",function () {
            this.style.backgroundColor = "hotpink";
        })
        ```

8. 动画

    - jQuery动画本质 : 在指定时间内不断改变元素样式值来实现的
               1. animate(): 自定义动画效果的动画
               2. stop(): 停止动画
    - 需求：
            1.宽度扩大
            2.宽度和高度扩大
            3.先宽度扩大后高度扩大

    - ```javascript
        
        // $("#btn1").click(function () {
        //     $(".div1").animate({
        //         "width":"200px",//写法1  ！！！！！！！！！！！！！！！！！！
        //         height:"200px"  //写法2  ！！！！！！！！！！！！！！！！！！
        //     },2000);
        // });//宽高同时扩大
        $("#btn1").click(function () {
            $(".div1")
                .animate({
                    width: 300     //写法3  ！！！！！！！！！！！！！！！！！！
                },2000)
                .animate({
                    height: "300px"
                },4000);
        });
        // 2.向右移动
        //     1.向右移动 固定的200px
        //     2.基于当前位置向右移动 200px
        // $("#btn2").click(function () {
        //     $(".div1").animate({
        //         left: "500px"
        //     },1000)
        // });
        $("#btn2").click(function () {
            $(".div1").animate({
                left: "+=200px"    //！！！！！！！！！！！！！！！！！！！！！！
            },1000)
        });
        // 3.向左移动
        //     1.向右移动 固定的200px
        //     2.基于当前位置向右移动 200px
        $("#btn3").click(function () {
            $(".div1").animate({
                left: "-=200px"    //！！！！！！！！！！！！！！！！！！！！！！
            },1000)
        });
        // 4.停止动画
        //     1.停止所有的动画
        //     2.停止当前动画，直接开始下一个动画
        //     3.停止并结束当前动画，开始下一个动画
        //一般情况下，在一个动画还没结束的时候就点击另一个动画的话，这些动画会排成队，等前边的完成动作才开始。
        $("#btn4").click(function () {
            $(".div1").stop(true,false);//停止所有动画，并且元素停止在当前状态下
        })
        ```

    -  <span style="color:orange">stop(blooean1,blooean2) blooean1：是否清空动画队列，blooean2：是否立即到达当前动画的最终状态，默认都是false</span>
         $(".div1").stop();//停止当前动画，并且在当前状态下继续执行下一个动画，不清空已经产生的动画队列
    - $(".div1").stop(true,true);//停止所有动画，并且元素立即到达当前动画的最终效果，并且清空后边的动画队列
    - $(".div1").stop(false,true);//停止当前动画，元素立即到达当前动画的最终效果，并且不清空队列，继续执行后续的动画队列

9. 插件扩展

    - 扩展jQuery的工具方法
        $.extend(object)

    2. 扩展jQuery对象的方法
        $.fn.extend(object)
        
    2. ```javascript
        $.extend({
            min: function (a, b) {
                return a < b ? a : b;
            },
            max: function (a, b) {
                return a > b ? a : b;
            },
            leftTrim: function (str) {
                return str.replace(/^\s+/,'');
            },
            rightTrim: function (str) {
                return str.replace(/\s+$/,"");
            }
        });
        
        
        $.fn.extend({
            checkAll: function () {
                this.prop("checked",true);
            },
            uncheckedAll: function () {
                this.prop("checked",false);
            },
            reverseChecked: function () {
                this.each(function () {
                    this.checked = !this.checked;
                })
            }
        });
        ```

10. 多库共存

    - ```javascript
        jQuery.noConflict();//释放$符号给其他库用
        ```

11. window.onload/$(function)
    - 区别: window.onload与 $ (document).ready()
        - window.onload
            - 包括页面的图片加载完后才会回调(晚)
            - 只能有一个监听回调
        - $(document).ready()
               - 等同于: $(function(){})
               - 加载完就回调(早)
               - 可以有多个监听回调