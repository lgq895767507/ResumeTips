#### retrofit源码流程分析

retrofit -- create() --> loadServiceMethod() --> ServiceMethod.parseAnnotations(this, method); -->  RequestFactory.parseAnnotations(retrofit, method);

--> 这里开始创建RequestFactory对象 并且调用了 RequestFactory.build() -->  parseMethodAnnotation(annotation);//解析方法注解，--> new RequestFactory() //生成RequestFactory对象

//上面的步骤将注解的method等设置到requestFactory对象中

 /**
   * Inspects the annotations on an interface method to construct a reusable service method that
   * speaks HTTP. This requires potentially-expensive reflection so it is best to build each service
   * method only once and reuse it.
   */
--> HttpServiceMethod.parseAnnotations(retrofit, method, requestFactory); //retrofit传进来等this对象， method 动态代理生成等对象方法, requestFactory上面初始化的对象

//返回类型ServiceMethod 

--> CallAdapter

--> Converter

//上面得到serviceMethod对象，当动态代理获得对象后调用了抽象类的invoke方法,即下面此实现类，进行网络请求调用了okhttp

//HttpServiceMethod中，调用这个方法的时候

 @Override final @Nullable ReturnT invoke(Object[] args) {
    Call<ResponseT> call = new OkHttpCall<>(requestFactory, args, callFactory, responseConverter);
    return adapt(call, args);
  }

  //adapter被CallAdapter调用，CallAdapter是个接口，几个实现类均调用了 call.enqueue(callback)，因此实现了网络请求。
  //RxJava2CallAdapter.adapt
  //RxJavaCallAdapter.adapt
  //BodyCallAdapter.adapt
  //ResponseCallAdapter.adapt