 #### 图层混合模式

  ```java

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        //1.ComposeShader
        //2.画笔Paint.setXfermode()
        //3.PorterDuffColorFilter

        //禁止硬件加速
        setLayerType(View.LAYER_TYPE_SOFTWARE, null);

        setBackgroundColor(Color.GRAY);

//        //离屏绘制
//        int layerId = canvas.saveLayer(0,0, getWidth(), getHeight(), mPaint, Canvas.ALL_SAVE_FLAG);

//        //目标图
        canvas.drawBitmap(createRectBitmap(mWidth, mHeight), 0, 0, mPaint);
//        //设置混合模式
        mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_IN));
//        //源图，重叠区域右下角部分
        canvas.drawBitmap(createCircleBitmap(mWidth, mHeight), 0, 0, mPaint);
//        //清除混合模式
        mPaint.setXfermode(null);
//
//        canvas.restoreToCount(layerId);

    }


```