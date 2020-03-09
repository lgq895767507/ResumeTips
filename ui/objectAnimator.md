#### 属性动画
  * vsync信号
  * 插值器：属性值从初始值过渡到结束值的变化规律
  * 估值器：属性值从初始值过渡到结束值的变化具体数值
  * ObjectAnimator是ValueAnimator的子类
  * ObjectAnimator objectAnimator = ObjectAnimator.ofFloat(textView, "translationX", -textView.getLeft(), mWidth, 0);
  * ValueAnimator.ofInt()