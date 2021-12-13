# 抖音数据采集教程，逆向研究X-Gorgon,X-Khronos算法协议

抖音的接口中，通过抓包抖音请求时，可以发现x-gorgon和 xlog两大参数是必备的，虽然说目前有些接口不通过这两个参数还是可以拿到数据，但是我觉得抖音已经预留显示了这两大请求的参数，只会逐步提高要求，用于服务器验证，不然日后都无法请求到数据。

## 通过工具逆向反编译出了抖音app的源码，发现下列这个位于类com.ss.sys.ces.gg.tt中，有这样一个函数用于生成x-gorgon的值。

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


```python
public static void init_gorgon()

    {
        com.bytedance.frameworks.baselib.network.http.e.j = new e.a()

        {
            public final Map<String, String> a(String paramAnonymousString, Map<String, List<String>> paramAnonymousMap)

            {
                HashMap localHashMap = new HashMap();

                for (;;)

                {
                    try

                    {
                        if ((!paramAnonymousString.toLowerCase().contains("http")) && (!paramAnonymousString.toLowerCase().contains("https"))) {
                            throw new NullPointerException("nein http/https");

                        }

                        if ((paramAnonymousString.toLowerCase().contains("X-Khronos")) && (paramAnonymousString.toLowerCase().contains("X-Gorgon"))) {
                            throw new NullPointerException("it was");

                        }

                        if (!tt.a(paramAnonymousString))

                        {
                            int i = (int)(System.currentTimeMillis() / 1000L);

                            paramAnonymousString = tt.b(paramAnonymousString);

                            localObject2 = null;

                            if ((paramAnonymousString != null) && (paramAnonymousString.length() > 0))

                            {
                                localObject1 = d.a(paramAnonymousString);

                                Iterator localIterator = paramAnonymousMap.entrySet().iterator();

                                paramAnonymousMap = null;

                                paramAnonymousString = paramAnonymousMap;

                                if (localIterator.hasNext())

                                {
                                    Object localObject4 = (Map.Entry)localIterator.next();

                                    localObject3 = localObject2;

                                    if (((String)((Map.Entry)localObject4).getKey()).toUpperCase().contains("X-SS-STUB")) {
                                        localObject3 = (String)((List)((Map.Entry)localObject4).getValue()).get(0);

                                    }

                                    localObject2 = localObject3;

                                    if (!((String)((Map.Entry)localObject4).getKey()).toUpperCase().contains("COOKIE")) {
                                        continue;

                                    }

                                    String str = (String)((List)((Map.Entry)localObject4).getValue()).get(0);

                                    localObject2 = localObject3;

                                    if (str == null) {
                                        continue;

                                    }

                                    localObject2 = localObject3;

                                    if (str.length() <= 0) {
                                        continue;

                                    }

                                    localObject4 = d.a(str);

                                    str = tt.c(str);

                                    localObject2 = localObject3;

                                    paramAnonymousMap = (Map<String, List<String>>)localObject4;

                                    if (str == null) {
                                        continue;

                                    }

                                    localObject2 = localObject3;

                                    paramAnonymousMap = (Map<String, List<String>>)localObject4;

                                    if (str.length() <= 0) {
                                        continue;

                                    }

                                    paramAnonymousString = d.a(str);

                                    StcSDKFactory.getInstance().setSession(str);

                                    localObject2 = localObject3;

                                    paramAnonymousMap = (Map<String, List<String>>)localObject4;

                                    continue;

                                }

                                localObject3 = new StringBuilder();

                                ((StringBuilder)localObject3).append(i);

                                localHashMap.put("X-Khronos", ((StringBuilder)localObject3).toString());

                                if (localObject1 == null) {
                                    break label572;

                                }

                                localObject3 = localObject1;

                                if (((String)localObject1).length() == 0) {
                                    break label572;

                                }

                                if (localObject2 == null) {
                                    break label579;

                                }

                                localObject1 = localObject2;

                                if (((String)localObject2).length() == 0) {
                                    break label579;

                                }

                                if (paramAnonymousMap == null) {
                                    break label586;

                                }

                                localObject2 = paramAnonymousMap;

                                if (paramAnonymousMap.length() == 0) {
                                    break label586;

                                }

                                if (paramAnonymousString == null) {
                                    break label593;

                                }

                                paramAnonymousMap = paramAnonymousString;

                                if (paramAnonymousString.length() == 0) {
                                    break label593;

                                }

                                if (b.a().a) {
                                    Calendar.getInstance().getTimeInMillis();

                                }

                                paramAnonymousString = new StringBuilder();

                                paramAnonymousString.append((String)localObject3);

                                paramAnonymousString.append((String)localObject1);

                                paramAnonymousString.append((String)localObject2);

                                paramAnonymousString.append(paramAnonymousMap);

                                paramAnonymousString = com.ss.a.b.a.a(com.ss.sys.ces.a.leviathan(i, com.ss.a.b.a.a(paramAnonymousString.toString())));

                                if (b.a().a) {
                                    Calendar.getInstance().getTimeInMillis();

                                }

                                localHashMap.put("X-Gorgon", paramAnonymousString);

                                return localHashMap;

                            }

                        }

                        else

                        {
                            throw new NullPointerException("filter_1");

                        }

                    }

                    catch (Throwable paramAnonymousString)

                    {
                        paramAnonymousString.getMessage().contains("filter_1");

                        return localHashMap;

                    }

                    Object localObject1 = null;

                    continue;

                    label572:

                    Object localObject3 = "00000000000000000000000000000000";

                    continue;

                    label579:

                    localObject1 = "00000000000000000000000000000000";

                    continue;

                    label586:

                    Object localObject2 = "00000000000000000000000000000000";

                    continue;

                    label593:

                    paramAnonymousMap = "00000000000000000000000000000000";

                }

            }

        };

    }
```


 

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944827961-ff28f39b-e353-4f8b-b4d6-ebcfaa822f9d.png#align=left&display=inline&height=288&name=image.png&originHeight=575&originWidth=1043&size=49415&status=done&style=none&width=521.5)
 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944841368-4c7fe511-f8f2-48ac-bbea-3f2683187710.png#align=left&display=inline&height=386&name=image.png&originHeight=772&originWidth=740&size=42427&status=done&style=none&width=370)
对应的就是底层.so文件了
通过分析知道，最终的x-gorgon的生成，是调用com.ss.sys.ces.a.leviathan函数，所以我们只要利用这个函数就能帮我们解决x-gorgon的生成，他里面的参数有两个
 public static native byte[] leviathan(int paramInt, byte[] paramArrayOfByte);
第一个是当前的时间戳，也就是X-Khronos
第二个字节组就一串字符串，由Url、postdata、cookies和sesseionid分别进行MD5加密组成的字串，做的二次请求。
所以也就发现了X-SS-STUB的值是将要post的数据做md5计算的值，如果是要做post的话，那这个值就需要带上。就能正常请求到数据了。目前有了点小成果，具体细节就不公开了。


 
