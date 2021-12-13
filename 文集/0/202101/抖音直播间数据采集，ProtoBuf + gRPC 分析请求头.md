# 抖音直播间弹幕采集，ProtoBuf + gRPC 分析请求头



# 概念
> Protobuf是Google protocol buffer的简称，是一种语言中立、平台无关、易于扩展的结构化数据序列化技术，可用于数据传输、存储等领域。
> 与Protoful类似的序列化技术还有XML、JSON、Thrift等，但Protoful更快、更小、更简单，且具备良好的兼容性。

**目前经常运用在 安卓直播弹幕等业务场景中**


>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

# 配置请求头

## 方法1
> 只设置客户端请求时附带的header

`类 io.grpc.stub.MetadataUtils，其中有个方法`
```
@ExperimentalApi("https://github.com/grpc/grpc-java/issues/1789")
  public static <T extends AbstractStub<T>> T attachHeaders(
      T stub,
      final Metadata extraHeaders) {
    return stub.withInterceptors(newAttachHeadersInterceptor(extraHeaders));
  }
```
**
自己封装后
```
private static <T extends AbstractStub<T>> T attachHeaders(T stub, final Map<String, String> headerMap) {
    Metadata extraHeaders = new Metadata();
    if (headerMap != null) {
        for (String key : headerMap.keySet()) {
            Metadata.Key<String> customHeadKey = Metadata.Key.of(key, Metadata.ASCII_STRING_MARSHALLER);
            extraHeaders.put(customHeadKey, headerMap.get(key));
        }
    }
    return MetadataUtils.attachHeaders(stub, extraHeaders);
}
```
**

# 方法2
> 支持设置客户端请求的header以及获取服务端返回结果中的header

- [官方demo](https://grpc.github.io/grpc-java/javadoc/io/grpc/ClientInterceptor.html)
- [官方完整Demo](https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/header/HeaderClientInterceptor.java)

## 1. 设置拦截器
```
class HeaderClientInterceptor implements ClientInterceptor {
    private static final String TAG = "HeaderClientInterceptor";
    private Map<String, String> mHeaderMap;
    public HeaderClientInterceptor(Map<String, String> headerMap) {
        mHeaderMap = headerMap;
    }
    @Override
    public <ReqT, RespT> ClientCall<ReqT, RespT> interceptCall(MethodDescriptor<ReqT, RespT> method,
                                                               CallOptions callOptions, Channel next) {
        return new SimpleForwardingClientCall<ReqT, RespT>(next.newCall(method, callOptions)) {
            @Override
            public void start(Listener<RespT> responseListener, Metadata headers) {
                /* put custom header */
                if (mHeaderMap != null) {
                    for (String key : mHeaderMap.keySet()) {
                        Metadata.Key<String> customHeadKey = Metadata.Key.of(key, Metadata.ASCII_STRING_MARSHALLER);
                        headers.put(customHeadKey, mHeaderMap.get(key));
                    }
                }
                Logger.i(TAG, "header send to server:" + headers);
                super.start(new SimpleForwardingClientCallListener<RespT>(responseListener) {
                    @Override
                    public void onHeaders(Metadata headers) {
                        /**
                         * if you don't need receive header from server,
                         * you can use {@link io.grpc.stub.MetadataUtils attachHeaders}
                         * directly to send header
                         */
                        Logger.i(TAG, "header received from server:" + headers);
                        super.onHeaders(headers);
                    }
                }, headers);
            }
        };
    }
}
```
_

## 2. 使用
```
Map<String, String> headerMap = new HashMap<>();
//...
ClientInterceptor interceptor = new HeaderClientInterceptor(headerMap);
//...
ManagedChannel managedChannel = ManagedChannelBuilder.forAddress(host, port).usePlaintext(true).build();
Channel channel = ClientInterceptors.intercept(managedChannel, interceptor);
// then create stub here by this channel
```
**

# 所以
要实现添加header 那么必须实现 `ClientInterceptor` 接口类中的 `interceptCall` 的方法

- 而且还要有一个 添加Header的具体方法
- 不管 grpc 怎么混淆都离不开这种配置模式


 
例如这种

 

 

 
[![](https://cdn.nlark.com/yuque/0/2021/png/97322/1612183626152-cf7623fb-cc84-488e-ab61-533bb067b6a8.png#align=left&display=inline&height=128&originHeight=128&originWidth=1298&size=0&status=done&style=none&width=1298)](https://static.zhangkunzhi.com/typecho/2020/11/25/75898451902823/1606275898.png)

 

 

 
注意他们的类型，很明显这种情况可以快速推论

 

- `z1.c.v.p.a.d.b.f.a.a()` 大概率就是混淆后的 `interceptCall`
- `z1.c.v.p.a.d.b.f.a.c()` 是添加请求头的函数






___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
