# View

## View的各种位置
![](http://img.blog.csdn.net/20160808154319878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
如图:
* X: view左边到父控件左边的距离 会跟随view移动而变化
* left:view左边到父控件左边的距离 不跟随view移动而变化
* translationX:view移动的距离
* rawX:view左边到屏幕左边的距离
* scrollX:view内容移动的距离

  X = left + translationX;

  translationX和scrollX的区别是前者是view的移动,后者是view内部的内容移动

#### TouchSlop
可滑动最小距离,常量 ViewConfiguration.get(getContext()).getScaledTouchSlop();
#### VelocityTracker、GestureDetector


## View的滑动
#### ScrollTo ScrollBy
这两个方法有什么区别?首先确定一点这两个方法移动的都只是view里的内容 而不是view本身
scrollTo():  View内容<font color=Crimson size=14>set</font>位置, 多次调用也是移动到同一位置
scrollBy():  View内容<font color=Crimson size=14>move</font>位置, 多次调用,内容会一直移动,超出view范围会看不见内容。

#### 动画,属性动画
#### 改变布局位置   改变LayoutParams
#### 弹性滑动 Scroller



## View事件分发
* dispatchTouchEvent(MotionEvent ev)

用来进行事件分发,如果事件能过传递给当前view,就一定会调用此方法。返回结果受当前View的onTouchEvent和下级View的dispatch方法的影响,表示是否消耗当前事件。
* onInterceptTouchEvent(MotionEvent ev)

在上述方法内部运行,用来判断是否拦截某事件,如果当前view拦截了某个事件,那么在同一事件序列中,不会被再次调用。返回结果表示是否拦截当前事件。
* onTouchEvent(MotionEvent ev)

在dispatch方法中调用,用来处理点击事件,返回结果表示是否消耗当前时间,如果不消耗,则在同一事件序列中,无法再接收到事件、

可以用如下伪代码理解这三个方法

        public boolean dispatchTouchEvent(MotionEvent ev) {
            boolean result = false;
            if (onInterceptTouchEvent(ev)) {
                result = onTouchEvent(ev);
            } else {
                result = child.dispatchTouchEvent(ev);
            }
            return result;
        }
关于事件传递的机制，有以下结论，可以更好的理解整个传递机制
* 1、同一个事件序列是指从手指接触屏幕的那一刻起，到手指离开屏幕的那一刻结束，在这个过程中所产生的的一系列事件，这个事件序列以down时间开始，中间含有数量不定的move事件，最终以up事件结束。
* 2、正常情况下，一个事件序列只能被一个View拦截且消耗。这一条的原因可以参考3，因为一旦一个元素拦截了某次事件，那么同一个事件序列内的所有事件都会直接交给它处理，因此同一个事件序列中的事件不能分别由两个View同时处理，但是通过特殊手段可以做到，比如一个View将本该自己处理的事件通过onTouchEvent强行传递给其他View处理。
* 3、某个View一旦决定拦截，那么这一个事件序列都只能由它来处理（如果事件序列能够传递给它的话），并且它的onInterceptTouchEvent不会再被调用。这条也很好理解，就是说当一个View决定拦截一个事件后，那么系统会把同一个事件序列内的其他方法都直接交给他来处理，因此就不用再调用这个View的onInterceptTouchEvent去询问它是否要拦截了。
* 4、某个View一旦开始处理事件，如果它不消耗ACTION_DOWN事件（onTouchEvent返回了false），那么同一事件序列中的其他事件都不会再交给它来处理，并且事件将重新由它的父元素去处理，即父元素的onTouchEvent会被调用。意思就是事件一旦交给一个View处理，那么它就必须消耗掉，否则同一事件序列中剩下的事件就不能再交给它来处理了，这就好比上级交给程序员一件事，如果这件事没有处理好，短期内上级就不敢再把事情交给这个程序员做了，二者是类似的道理。
* 5、如果View不消耗除ACTION_DOWN以外的其他事件，那么这个点击事件会消失，此时父元素的onTouchEvent并不会被调用，并且当前View可以持续收到后续的事件，最终这些消失的点击事件会传递给Activity处理。
* 6、ViewGroup默认不拦截任何事件。Android源码中ViewGroup的onInterceptTouchEvent方法默认返回false。
* 7、View没有onInterceptTouchEvent方法，一旦有点击事件传递给它，那么它的onTouchEvent方法就会被调用。
* 8、View的onTouchEvent默认都会消耗事件（返回true），除非它是不可点击的（clickable和longClickable同时为false）。View的logClickable属性默认都为false，clickable属性要分情况，比如Button的clickable属性默认为true，而TextView的clickable属性默认为false。
* 9、View的enable属性不影响onTouchEvent的默认返回值。哪怕一个View是disable状态的，只要它的clickable或者longClickable有一个为true，那么它的onTouchEvent就返回true。
* 10、onClick会发生的前提是当前View是可点击的，并且它收到了down和up的事件。
* 11、事件传递过程是由外向内的，即事件总是先传递给父元素，然后再由父元素分发给子View，通过requestDisallowInterceptTouchEvent方法可以再子元素中干预父元素的事件分发过程，但是ACTION_DOWN事件除外。


## View 滑动冲突
* 外部拦截法
* 内部拦截法

# View的工作原理
* measure
* layout
* draw
* 自定义View
    * 继承View重写onDraw()方法
    * 继承ViewGroup派生特殊的Layout
    * 继承特定的View(比如TextView)
    * 继承特定的ViewGroup(比如LinearLayout)
