#### Canvas离屏缓冲

* 离屏缓冲：通过离屏缓冲，把想要绘制的内容单独绘制在缓冲层，保证Xfremode的使用不会出错错误的结果。

```java

        //使用离屏绘制，可以做短时的离屏缓冲，在绘制之前保存，绘制之后恢复
        int layerID = canvas.saveLayer(0, 0, getWidth(), getHeight(), mPaint, Canvas.ALL_SAVE_FLAG);

        //先将路径绘制到 bitmap上
        Canvas dstCanvas = new Canvas(mDstBmp);
        dstCanvas.drawPath(mPath, mPaint);

        //绘制 目标图像
        canvas.drawBitmap(mDstBmp, 0, 0, mPaint);
        //设置 模式 为 SRC_OUT, 擦橡皮区域为交集区域需要清掉像素
        mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_OUT));
        //绘制源图像
        canvas.drawBitmap(mSrcBmp, 0, 0, mPaint);

        mPaint.setXfermode(null); //用完及时清除Xfremode

        canvas.restoreToCount(layerID);


```

#### canvas

```java

/        //1，平移操作
//        canvas.drawRect(0,0, 400, 400, mPaint);
//        canvas.translate(50, 50);
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(0,0, 400, 400, mPaint);
//        canvas.drawLine(0, 0, 600,600, mPaint);

//        //缩放操纵
//        canvas.drawRect(200,200, 700,700, mPaint);
////        canvas.scale(0.5f, 0.5f);
//        //先translate(px, py),再scale(sx, sy),再反响translate
//        //canvas.scale(0.5f, 0.5f, 200,200);
//
//        canvas.translate(200, 200);
//        canvas.scale(0.5f, 0.5f);
//        canvas.translate(-200, -200);
//
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(200,200, 700,700, mPaint);
//        canvas.drawLine(0,0, 400, 600, mPaint);

//        //旋转操作
//        canvas.translate(50,50);
//        canvas.drawRect(0,0, 700,700, mPaint);
//        canvas.rotate(45);
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(0,0, 700,700, mPaint);

//        canvas.drawRect(400, 400, 900, 900, mPaint);
//        canvas.rotate(45, 650, 650); //px, py表示旋转中心的坐标
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(400, 400, 900, 900, mPaint);

//        //倾斜操作
//        canvas.drawRect(0,0, 400, 400, mPaint);
////        canvas.skew(1, 0); //在X方向倾斜45度,Y轴逆时针旋转45
//        canvas.skew(0, 1); //在y方向倾斜45度， X轴顺时针旋转45
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(0, 0, 400, 400, mPaint);

        //切割
//        canvas.drawRect(200, 200,700, 700, mPaint);
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(200, 800,700, 1300, mPaint);
//        canvas.clipRect(200, 200,700, 700); //画布被裁剪
//        canvas.drawCircle(100,100, 100,mPaint); //坐标超出裁剪区域，无法绘制
//        canvas.drawCircle(300, 300, 100, mPaint); //坐标区域在裁剪范围内，绘制成功

//        canvas.drawRect(200, 200,700, 700, mPaint);
//        mPaint.setColor(Color.GRAY);
//        canvas.drawRect(200, 800,700, 1300, mPaint);
//        canvas.clipOutRect(200,200,700,700); //画布裁剪外的区域
//        canvas.drawCircle(100,100,100,mPaint); //坐标区域在裁剪范围内，绘制成功
//        canvas.drawCircle(300, 300, 100, mPaint);//坐标超出裁剪区域，无法绘制

        //矩阵
        canvas.drawRect(0,0,700,700, mPaint);
        Matrix matrix = new Matrix();
//        matrix.setTranslate(50,50);
//        matrix.setRotate(45);
        matrix.setScale(0.5f, 0.5f);
        canvas.setMatrix(matrix);
        mPaint.setColor(Color.GRAY);
        canvas.drawRect(0,0,700,700, mPaint);


          /**
         * 1.canvas内部对于状态的保存存放在栈中
         * 2.可以多次调用save保存canvas的状态，并且可以通过getSaveCount方法获取保存的状态个数
         * 3.可以通过restore方法返回最近一次save前的状态，也可以通过restoreToCount返回指定save状态。指定save状态之后的状态全部被清除
         * 4.saveLayer可以创建新的图层，之后的绘制都会在这个图层之上绘制，直到调用restore方法
         * 注意：绘制的坐标系不能超过图层的范围， saveLayerAlpha对图层增加了透明度信息
         */



```