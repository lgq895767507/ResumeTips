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