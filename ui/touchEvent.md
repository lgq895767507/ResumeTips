#### 事件分发机制
  * viewGroup事件分发，可以用以下伪代码表示：

```java

public boolean dispatchTouchEvent(MotionEvent ev){
    boolean consume = false;
    //调用onInterceptTouchEvent判断是否拦截
    if(onInterceptTouchEvent(ev)){
        //拦截则调用自身的onTouchEvent
        consume = onTouchEvent(ev);
    }else{
        //不拦截，将事件分发给子view
        consume = child.dispatchTouchEvent(ev);
    }
    return consume;
}

```

![avatar](/img/event_dispatcher.png)


#### view的事件分发
* OnTouch > OnTouchEvent > onClickListener
* onClick是在down事件做判断的，当onLongClick返回不为true，且可点击，才会收到回调事件
* onLongClick是在down事件的时候会添加长按事件的监听，如果up事件响应的时候，满足长按事件，会移除事件并且执行 onLongClick回调。