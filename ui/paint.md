#### Paint画笔高级应用

```java

        mPaint = new Paint(); //初始化
        mPaint.setColor(Color.RED);// 设置颜色
        mPaint.setARGB(255, 255, 255, 0); // 设置 Paint对象颜色,范围为0~255
        mPaint.setAlpha(200); // 设置alpha不透明度,范围为0~255
        mPaint.setAntiAlias(true); // 抗锯齿
        mPaint.setStyle(Paint.Style.FILL); //描边效果
        mPaint.setStrokeWidth(4);//描边宽度
        mPaint.setStrokeCap(Paint.Cap.ROUND); //圆角效果
        mPaint.setStrokeJoin(Paint.Join.MITER);//拐角风格
        mPaint.setShader(new SweepGradient(200, 200, Color.BLUE, Color.RED)); //设置环形渲染器
        mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DARKEN)); //设置图层混合模式
        mPaint.setColorFilter(new LightingColorFilter(0x00ffff, 0x000000)); //设置颜色过滤器
        mPaint.setFilterBitmap(true); //设置双线性过滤
        mPaint.setMaskFilter(new BlurMaskFilter(10, BlurMaskFilter.Blur.NORMAL));//设置画笔遮罩滤镜 ,传入度数和样式
        mPaint.setTextScaleX(2);// 设置文本缩放倍数
        mPaint.setTextSize(38);// 设置字体大小
        mPaint.setTextAlign(Paint.Align.LEFT);//对其方式
        mPaint.setUnderlineText(true);// 设置下划线

        String str = "Android高级工程师";
        Rect rect = new Rect();
        mPaint.getTextBounds(str, 0, str.length(), rect); //测量文本大小，将文本大小信息存放在rect中
        mPaint.measureText(str); //获取文本的宽
        mPaint.getFontMetrics(); //获取字体度量对象


```



 







