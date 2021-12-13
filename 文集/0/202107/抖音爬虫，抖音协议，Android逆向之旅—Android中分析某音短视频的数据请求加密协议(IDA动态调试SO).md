> 免责申明：此内容仅供学习交流使用，不得用于商业用途，如果涉及侵权，联系作者删除
> 交流v：1764328791




# 一、前言
最近萌发了一个做app的念头，大致什么样的app先暂时不说，后面会详细介绍这个app的开发流程和架构，不过先要解决一些技术前提问题，技术问题就是需要分析解密当前短视频四小龙：某音，某山，某拍，某手这四家短视频的数据请求加密协议，只有破解了加密协议，才能自定义数据请求拉回数据。不多说了，本文先来第一站搞定某音和某山这两个数据请求的加密协议，为什么说是这两个呢？因为我们在后面分析会发现这两个app其实用的是一套加密协议，毕竟这两个app都是某头条内部孵化的项目。所以只要搞定一个即可。我们决定搞定某音吧，毕竟我比较看好和喜爱看某音。
​


# **二、逆向分析**
不多说了，赶紧来分析吧，不过本文不在利用粗暴的静态方式去破解了，应广大同学要求，就介绍IDA动态调试so来进行破解，这样也能给大家带来IDA的使用技巧。毕竟我写文章技术都是为了你们。这种分析请求数据的突破口一般都是抓包，这不用多说了。不过都是用了https请求，所以需要手动在设备中安装Fiddler证书，才能抓到正确的数据信息：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980265-1afd5cf7-db62-4170-86d5-e69bd6fa0d01.png)
打开某音之后，看到数据刷的很快，发现一个feed的接口是返回的首页的数据，在分析它的请求参数中有三个字段是minCursor,maxCursor,count其实这三个字段就是后面他进行数据分页请求的关键，到后面再详细说。不过这里看到还没有什么问题，不过问题往下看他的更多请求参数，会发现两个字段：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980322-635b1a06-ad0e-47d2-a55f-66143a46f5b6.png)
这时候会发现其他参数都和本地设备有关或者直接写死的，唯独这两个参数信息是始终变化的。所以猜想这两个字段是用于请求协议数据加密和服务端进行校验的。那么如果我们想单独构造信息去请求，这两个字段的信息一定要正确，不然请求不到正确的数据的。好了，这里简单了，使用Jadx打开某音app即可，直接全局搜索字段的信息”as=”：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980310-1b820509-b24a-4b73-b903-55f3a36a2433.png)
看到这里是构造了as和cp两个字段的值，直接点击进入即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980270-ec7bdcbb-2d03-437a-a953-05111d95d139.png)
这里大致的逻辑也比较简单，利用UserInfo.getUserInfo函数获取字符串，然后对半开给as和cp两个字段，右移操作就是除以2的意思。这里不分析代码来看看参数怎么来的，直接用Xposed进行hook这个函数打印参数看结果，粗暴快捷，以后其实这都是快速解决问题的一种方式，hook大法是最无敌的：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980405-096e68e2-5b42-478a-a12c-7a7cf31584a5.png#clientId=ubf00372a-92b6-4&from=paste&id=uae34bfd6&originHeight=156&originWidth=601&originalType=url&ratio=1&status=done&style=none&taskId=uf924429f-0050-4fd2-9799-5dcbe960972)
先来看一下这个加密方法的参数信息，看到是native的，也就是说加密操作是在so中做的，后面需要调试的也是这个so了。也不管了，hook这个方法然后打印信息看参数构造：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980756-2418e771-e3f6-4658-8c19-ef9840a684a6.png#clientId=ubf00372a-92b6-4&from=paste&id=ua45bab06&originHeight=671&originWidth=874&originalType=url&ratio=1&status=done&style=none&taskId=u78fffdbc-c498-4142-a92d-a52422ba596)
直接运行模块，打印日志看信息：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981172-e1838851-a6f4-4989-90ce-015426c70ffa.png#clientId=ubf00372a-92b6-4&from=paste&id=uf6a92eb4&originHeight=291&originWidth=868&originalType=url&ratio=1&status=done&style=none&taskId=u7a1227af-c450-470a-af28-865cfaf3a4d)
看到三个参数打印的值，发现大致三个参数的意义是：当前时间戳，请求url，请求参数的数组信息。好了，我们现在可以新建一个Android工程，然后构造这三个参数信息，然后调用它的so函数，为了找到这个so名称，**全局搜索字符串信息”System.loadLibrary”**即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609980931-4503dfa1-b860-4fb6-9dc4-595e2355fa10.png#clientId=ubf00372a-92b6-4&from=paste&id=u378a9d8f&originHeight=249&originWidth=769&originalType=url&ratio=1&status=done&style=none&taskId=u9124080a-5bde-407a-bec6-e28737c0dc2)
这样就找到了这个so名称了，到libs目录下把这个so拷贝出来到我们自己构建的demo工程中，这里先不着急调试去分析native的加密函数功能，还是老规矩，拿来主义，直接上层构造一个和他一样的包名的native函数，调用它的so获取结果即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981138-8f9c4f49-f7e3-4ab8-b4d0-a64b783784e7.png#clientId=ubf00372a-92b6-4&from=paste&id=u88cfbf17&originHeight=468&originWidth=893&originalType=url&ratio=1&status=done&style=none&taskId=udaf21440-b8b4-4736-85c0-007e42a6b92)
然后就开始构造参数信息了，这里为了简单，先把那些公共的参数信息写死，比如设备的sid，aid，版本号等信息：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981151-90b14661-9e63-4cd0-811f-28b8fd732b1d.png#clientId=ubf00372a-92b6-4&from=paste&id=u9aadf680&originHeight=569&originWidth=833&originalType=url&ratio=1&status=done&style=none&taskId=u7c2956ff-62d9-4855-bda4-fcfed0cf81b)
这里我们利用公众参数信息，构造请求字段数组信息，然后还有单独的几个字段值不能参与操作，可以通过之前的打印日志分析出来。当然时间戳也是服务器格式，和本地是少三位的，直接除以1000即可。下面为了简单直接把公众参数写死即可，当然后续优化就是把这些参数值进行动态获取填充即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981416-b89fa5a4-faa1-4be0-b76b-28101b7901c6.png#clientId=ubf00372a-92b6-4&from=paste&id=u5fdc24f6&originHeight=554&originWidth=626&originalType=url&ratio=1&status=done&style=none&taskId=u4ded42c6-bf42-4b79-98de-82dd72c7156)
构造好参数之后，然后就开始调用native函数，然后获取返回结果即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981463-4fda76cf-9f3e-491c-87cd-5eedf35d4327.png#clientId=ubf00372a-92b6-4&from=paste&id=u6094935a&originHeight=372&originWidth=648&originalType=url&ratio=1&status=done&style=none&taskId=u194a547e-7089-4cb9-9bbc-aee207a219f)
到这里，我们构造的工作就完成了，下面就开始直接运行吧，不过可惜的是，没有这么顺利，直接运行闪退了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981666-a2e198e7-6f5d-4c8b-9a43-14bbe9af7d89.png#clientId=ubf00372a-92b6-4&from=paste&id=u7f6523c5&originHeight=103&originWidth=858&originalType=url&ratio=1&status=done&style=none&taskId=ua433f689-cdf1-41ba-85d6-d534a30e7d3)
这里应该进行加载so出现问题了，那么我在回过头看看我们是不是有些环境没初始化，看看UserInfo类中是不是还有一些初始化方法没调用：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981972-d8e140ba-34a6-4b5c-867e-8531f686cc02.png#clientId=ubf00372a-92b6-4&from=paste&id=u7c8750e2&originHeight=150&originWidth=629&originalType=url&ratio=1&status=done&style=none&taskId=ua862b42a-5a1e-49c3-99e0-d9fd3944273)
这里看到的确有两个方法有点可疑，全局查看这两个方法的调用地方：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982045-3759eaef-60c3-4dcb-b2b7-dc67c6c34467.png#clientId=ubf00372a-92b6-4&from=paste&id=u1fced47b&originHeight=186&originWidth=760&originalType=url&ratio=1&status=done&style=none&taskId=u905c61f4-d102-424f-83fc-42f6e9b147e)
看到全局中就一个地方调用了setAppId方法，而且值就是写死的为2，另一个方法initUser就有点麻烦了，不过还是万能的hook大法，直接hook这个方法打印他的参数信息即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609981962-0774d929-dbd5-4abf-b84c-32aa16a51a38.png#clientId=ubf00372a-92b6-4&from=paste&id=u7953ebf4&originHeight=338&originWidth=777&originalType=url&ratio=1&status=done&style=none&taskId=u918b1ee4-2689-421f-a4d3-a54260a7bdd)
运行模块，查看打印的日志信息：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982255-1a7e417f-fe42-4835-a675-cd2a10026892.png#clientId=ubf00372a-92b6-4&from=paste&id=u4ab50019&originHeight=83&originWidth=819&originalType=url&ratio=1&status=done&style=none&taskId=u81803269-732e-447f-89ce-2248861b5cc)
看到了，参数信息一致都是这个字符串信息，直接拷贝出来赋值调用即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982060-1be75f32-9b49-4e92-8133-e9cdec8366da.png#clientId=ubf00372a-92b6-4&from=paste&id=ue0de36b7&originHeight=196&originWidth=909&originalType=url&ratio=1&status=done&style=none&taskId=u8a43b16f-3e05-4280-9045-7d3434044a8)
​


# **三、IDA调试SO**
在次运行，发现可惜了，还是报错，而且诡异的是日志没打印，也就说setAppId和initUser函数没有调用就退出了，那么就会想到？是不是so中的JNI_OnLoad函数中做了一些判断逻辑呢？直接用IDA打开这个so文件即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982448-ada3bdde-4f37-4b15-aa11-eb1ae5bbf0be.png#clientId=ubf00372a-92b6-4&from=paste&id=u7c172c35&originHeight=762&originWidth=527&originalType=url&ratio=1&status=done&style=none&taskId=u227f643a-bd11-434b-b330-c55d707e2d4)
打开之后找到JNI_OnLoad函数，F5查看他的C代码，发现果然内部有很多判断，然后直接调用exit函数退出了。所以如果想成功的调用它的so方法，得先过了他的这些判断了，这里就要开始进行调试操作了，其实这里可以直接静态分析有一个快捷的方法，但是为了给大家介绍动态调试so技巧，就多走点弯路吧，后面再说一下粗暴简单的技巧。
下面就来开始进行调试so文件了，关于IDA调试so文件，我之前已经写过一篇非常详细的文章了：[Android中IDA动态调试so文件详解](http://www.wjdiankong.cn/android%E9%80%86%E5%90%91%E4%B9%8B%E6%97%85-%E5%8A%A8%E6%80%81%E6%96%B9%E5%BC%8F%E7%A0%B4%E8%A7%A3apk%E8%BF%9B%E9%98%B6%E7%AF%87ida%E8%B0%83%E8%AF%95so%E6%BA%90%E7%A0%81/)；这里不会在详细介绍具体步骤了，直接上手干：
**第一步：拷贝IDA安装目录下的android_server文件到设备目录下**
**第二步：运行android_server命令**
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982527-9be489ee-7198-48dc-8155-552472675300.png#clientId=ubf00372a-92b6-4&from=paste&id=u34cbf431&originHeight=182&originWidth=591&originalType=url&ratio=1&status=done&style=none&taskId=u02db3c1d-fb77-4b74-b8bd-d555c2cfa8b)
**第三步：转发端口和用debug模式打开应用**
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982532-5ca7ba2c-3a7e-4394-ad81-07a1c3ff66de.png#clientId=ubf00372a-92b6-4&from=paste&id=ub08c9fe4&originHeight=99&originWidth=597&originalType=url&ratio=1&status=done&style=none&taskId=ud84ffb5c-d0ea-4b53-91d4-d8a1b1f8c6a)
这里有同学好奇为什么不需要修改xml中的debug属性呢？因为我调试的是我自己的demo工程，而Eclipse默认签名打包出来的apk这个属性值就是true的，所以不需要进行修改了。
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982745-5976c498-3e50-4eb7-9b10-5e36d4b293b8.png#clientId=ubf00372a-92b6-4&from=paste&id=u9aa99564&originHeight=136&originWidth=195&originalType=url&ratio=1&status=done&style=none&taskId=u5f3f9d02-a487-4a0c-9ba5-61fd829803e)
**第四步：启动IDA附件进程**
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982739-6d6bcd5b-97b9-4d26-b1b2-58ea7ba573f6.png#clientId=ubf00372a-92b6-4&from=paste&id=u69858bc8&originHeight=343&originWidth=432&originalType=url&ratio=1&status=done&style=none&taskId=u08670ab6-bd67-4000-818f-f15157cf6f0)
设置本地地址和一些选项：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609982998-230c9a76-91dc-4b63-a538-75867900ca9f.png#clientId=ubf00372a-92b6-4&from=paste&id=u7778fdf3&originHeight=466&originWidth=885&originalType=url&ratio=1&status=done&style=none&taskId=uce8f2b2e-1247-410c-a445-8af26b1e6dd)
因为现在已经知道需要调试JNI_OnLoad函数，所以需要设置挂起load函数处，然后选择调试的应用进程：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983394-8c6f18f1-e3d4-463e-a411-c13ea2c4037e.png#clientId=ubf00372a-92b6-4&from=paste&id=u07627a94&originHeight=200&originWidth=629&originalType=url&ratio=1&status=done&style=none&taskId=ub4f330eb-5e95-4f48-a7fc-98c7808c87e)
**第五步：查看调试端口连接调试器**
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983038-3456b384-4f11-4f49-966e-2c9e9ed39f64.png#clientId=ubf00372a-92b6-4&from=paste&id=u7fe35fe9&originHeight=90&originWidth=560&originalType=url&ratio=1&status=done&style=none&taskId=uea511924-6ef1-46e6-988f-6f6d33946bc)
在Eclipse中的DDMS中查看有红色小蜘蛛的调试应用端口号是8647，然后连接调试器：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983288-6936b065-2e02-41a1-9008-76c91831ece2.png#clientId=ubf00372a-92b6-4&from=paste&id=u38e3593f&originHeight=184&originWidth=743&originalType=url&ratio=1&status=done&style=none&taskId=u5d730f96-c4b5-40f0-b82e-f58d07164a7)
这里一定注意端口正确，不然链接操作的。
**第六步：点击IDA中的运行按钮，或者F9快捷键**
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983310-e599a36b-79e7-43a5-b6bd-6ed18e59e0c7.png#clientId=ubf00372a-92b6-4&from=paste&id=u5d3d3f32&originHeight=235&originWidth=583&originalType=url&ratio=1&status=done&style=none&taskId=u4e054e2c-4e23-4ef0-956d-58e4e27a561)
这时候发现红色蜘蛛变成绿色了，而且调试对话框也没有了。这时候就进入调试页面了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983696-791312ad-a726-4406-ad4e-c63b190251bc.png#clientId=ubf00372a-92b6-4&from=paste&id=u2f94071a&originHeight=100&originWidth=588&originalType=url&ratio=1&status=done&style=none&taskId=u3bce2cb6-1a5f-4360-811e-761afe808f9)
为了安全起见，再一次查看debug选项有没有挂起load函数：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983618-9e1b3a76-ec5f-44ba-abf0-f9372cd4cc66.png#clientId=ubf00372a-92b6-4&from=paste&id=u508b2535&originHeight=572&originWidth=515&originalType=url&ratio=1&status=done&style=none&taskId=uc70a7311-8d85-4cb7-8aed-d2380427f9e)
如果发现没有，还需要进行手动勾选上：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983850-302e9301-9802-40b0-ad28-c9a4ca78401a.png#clientId=ubf00372a-92b6-4&from=paste&id=u651425e3&originHeight=469&originWidth=471&originalType=url&ratio=1&status=done&style=none&taskId=u5e07fe27-e90d-4d2b-a66c-b757b2e341a)
因为我们给JNI_OnLoad函数挂起了，而一个应用会加载很多系统的so文件，所以这里一直点击运行或者F9：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984203-7cf6b88f-aed9-40c0-b485-11c896d2a107.png#clientId=ubf00372a-92b6-4&from=paste&id=u6bbb9f4e&originHeight=235&originWidth=583&originalType=url&ratio=1&status=done&style=none&taskId=u8c5df9ec-6d7a-4db2-af7f-9092dcf49a3)
过了系统so加载步骤：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609983856-44e92b16-2bb6-4c58-83ed-6bfd06728e31.png#clientId=ubf00372a-92b6-4&from=paste&id=u46f2622f&originHeight=185&originWidth=522&originalType=url&ratio=1&status=done&style=none&taskId=u1407a0d5-1952-4091-8d67-ca0accb1bd7)
这些都是系统的so文件加载，所以一路往下都直接运行越过调试即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984104-3674091c-5f49-4227-b356-d86be8141d6f.png#clientId=ubf00372a-92b6-4&from=paste&id=ub6c62cab&originHeight=387&originWidth=420&originalType=url&ratio=1&status=done&style=none&taskId=ue3d1a128-48d0-4a7a-ac89-a5389150d09)
可以看到这里会加载很多系统的so文件，当我们点击应用的按钮，加载自己的libuserinfo.so文件的时候才开始进行调试工作：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984310-fc06a411-e209-4914-98ae-3bfa150a7088.png#clientId=ubf00372a-92b6-4&from=paste&id=u47da416f&originHeight=185&originWidth=624&originalType=url&ratio=1&status=done&style=none&taskId=u7b98fe3f-5a70-4c1e-8491-51de73a2dfb)
点击OK加载进来，然后就停留在了挂起状态了，这时候，我们在右侧栏查找这个so文件：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984481-58d727d0-0c7b-4e3a-b0c6-f005d8695428.png#clientId=ubf00372a-92b6-4&from=paste&id=u7a5893c6&originHeight=184&originWidth=575&originalType=url&ratio=1&status=done&style=none&taskId=u53f10923-96c6-490c-916f-1306290d750)
然后点击，继续查找他的JNI_OnLoad函数：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984418-a3d49d2e-b5a6-41c3-8968-017347374ff4.png#clientId=ubf00372a-92b6-4&from=paste&id=u9086ab69&originHeight=153&originWidth=573&originalType=url&ratio=1&status=done&style=none&taskId=u569b7146-dee9-437a-a404-9722a5ab8fa)
点击进入JNI_OnLoad函数处，下个断点：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984676-7127fe31-1015-4b7e-8694-57055e195891.png#clientId=ubf00372a-92b6-4&from=paste&id=uc37d6aae&originHeight=539&originWidth=672&originalType=url&ratio=1&status=done&style=none&taskId=u8dae78ac-caf6-4631-bd43-b58893ecba8)
因为我们在之前静态分析了这个函数内部有很多个exit函数，为了好定位是哪个地方exit了，所以在每个exit函数之前下个断点，来判定退出逻辑，这里下断点有技巧，因为是if语句，所以在arm指令中肯定就是CMP指令之后的BEQ跳转，所以在每个CMP指令下个断点即可，这里发现了9个地方，所以下了断点也很多，慢慢分析：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984796-526aae6d-d316-46bb-b562-f50ca46393c4.png#clientId=ubf00372a-92b6-4&from=paste&id=ub63c142a&originHeight=144&originWidth=769&originalType=url&ratio=1&status=done&style=none&taskId=uc912b454-7edb-40e6-85a9-938782cc924)
第一处的CMP指令下个断点，然后运行到此处，查看R3寄存器值是否为0：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609984913-0109fb77-3915-4c99-8711-cb76cb1dc461.png#clientId=ubf00372a-92b6-4&from=paste&id=u8154a52e&originHeight=268&originWidth=812&originalType=url&ratio=1&status=done&style=none&taskId=ua9bd7f1e-d8d0-43fa-b293-e075c520e0b)
是0，那么第一处exit就没问题，接着往下走，直接按F9到下一个CMP判断指令断点处：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985132-0f5dd716-0271-478c-92b2-6b62769dc722.png#clientId=ubf00372a-92b6-4&from=paste&id=uaa7628af&originHeight=154&originWidth=829&originalType=url&ratio=1&status=done&style=none&taskId=u64d2129a-a9ee-4794-a7ff-3d0505f7ae3)
发现第二处的CMP中的R3寄存器值也是0，所以也没问题，直接F9到下一个断点：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985238-656de78c-24f2-49cf-87a1-136be58a33b5.png#clientId=ubf00372a-92b6-4&from=paste&id=u33dff41f&originHeight=326&originWidth=781&originalType=url&ratio=1&status=done&style=none&taskId=u3f40798b-843e-4ccf-9dbe-87bc35a00a9)
到了第三处判断发现R3寄出去你的值不是0了，而是1，所以为了继续能够往下走，就修改R3寄存器值即可，在右侧栏的寄存器中右击R3寄存器，然后点击修改值：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985213-4fb8c481-e14e-4fd5-bee2-7dce21ff90da.png#clientId=ubf00372a-92b6-4&from=paste&id=u700fd805&originHeight=513&originWidth=1023&originalType=url&ratio=1&status=done&style=none&taskId=u9a3e0e4a-72df-422d-9467-0a5fcc8b8f2)
把1改成0，保存即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985415-9eacf457-4240-4498-a5dd-b3065a0cc786.png#clientId=ubf00372a-92b6-4&from=paste&id=ub3f2e900&originHeight=170&originWidth=428&originalType=url&ratio=1&status=done&style=none&taskId=u2c5b27e0-58d1-4b3c-9d80-51aaecf30b1)
修改成功了，运行发现就过了判断：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985418-75f56e60-8fb9-44eb-abaa-9094f5d1a07f.png#clientId=ubf00372a-92b6-4&from=paste&id=ua2588957&originHeight=425&originWidth=954&originalType=url&ratio=1&status=done&style=none&taskId=u7332f3ff-e84a-495f-9d70-1303ab8ad60)就继续往下走，到下一个断点：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985587-69783a0d-205a-4267-9a0f-c1fe2b61827d.png#clientId=ubf00372a-92b6-4&from=paste&id=ud6e86d6f&originHeight=151&originWidth=694&originalType=url&ratio=1&status=done&style=none&taskId=ued4191e4-4d17-417f-bc57-aaa81d8b6ea)
这里有问题了，我们如果直接F9到第四处CMP的话，发现直接退出调试了，说明在3和4处判断中间有问题了，最终发现是这个跳转指令出现的问题，这里需要多次单步调试F7键，定位出现问题的地方了，我们进入这个跳转地址：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985743-e369b0b6-3511-42e0-ad1a-512fde05327f.png#clientId=ubf00372a-92b6-4&from=paste&id=u9d94139c&originHeight=314&originWidth=641&originalType=url&ratio=1&status=done&style=none&taskId=u89b89892-7ba7-42e6-b1d3-fa81e497b06)
然后发现内部还有BL指令，出现问题了，继续深入查看：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985814-ed323baa-ff5b-46ad-8edc-8aa9e94159b9.png#clientId=ubf00372a-92b6-4&from=paste&id=ua5a2362d&originHeight=567&originWidth=1082&originalType=url&ratio=1&status=done&style=none&taskId=u1c725a23-0523-43f1-93a3-2f6a93713a9)
看到了，这里发现问题的原因了，有一个GlobalContext类，这个类应该是Java层的，native层应该用反射机制获取全局的Context变量，我们直接去Jadx中搜索这个类：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985925-993cb860-f16e-4d2f-a12a-1e66e5f3fe05.png#clientId=ubf00372a-92b6-4&from=paste&id=u9916f506&originHeight=246&originWidth=397&originalType=url&ratio=1&status=done&style=none&taskId=ufcf0bc45-2d07-430b-a4a0-cf57aaf3da7)
那么问题就清楚了，因为我们的demo工程中压根没这个类，所以native中获取全局context变量就失败了，所以就exit失败退出了，解决办法也简单，直接在demo工程中构造这个类，然后在Application中初始化context即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609985854-d05cbfdd-30db-406a-918b-fe31a192cd4c.png#clientId=ubf00372a-92b6-4&from=paste&id=u9acf6519&originHeight=319&originWidth=873&originalType=url&ratio=1&status=done&style=none&taskId=uc96d686e-2949-4eff-b227-5726b45277b)
构造的时候一定要注意包名一致，然后在demo中的Application类进行设置context即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986050-8a63eac7-7501-436e-b552-8c3ebfd7a559.png#clientId=ubf00372a-92b6-4&from=paste&id=u01666dd1&originHeight=142&originWidth=564&originalType=url&ratio=1&status=done&style=none&taskId=uf13a0ad1-1ad8-48ec-9818-ad70e6ef9af)
然后再次运行项目，可惜还是不行，所以还得进行调试JNI_OnLoad函数了，方法步骤和上面类似，不多说了，不过这里应该是过了上面的Context获取失败的问题了，继续往下走，发现了重要信息了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986182-ec0d7cfe-996c-4265-9b85-fab5672c295c.png#clientId=ubf00372a-92b6-4&from=paste&id=u74f79d7c&originHeight=385&originWidth=895&originalType=url&ratio=1&status=done&style=none&taskId=u3b27e19d-b77a-475d-965a-cd525bfece5)
这里有一个类似于MD5的值，继续往下走BL处：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986615-234b9060-a023-46ae-95bb-f79f4e37ef65.png#clientId=ubf00372a-92b6-4&from=paste&id=u484c5897&originHeight=302&originWidth=948&originalType=url&ratio=1&status=done&style=none&taskId=uecf9e1fb-a47c-4682-b0b5-6bdee2be1e1)
看到R0寄存器中的值，发现是demo应用的签名信息，继续往下走BLX看看：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986475-038f7412-9449-49ac-9ed8-cecf871b0ff2.png#clientId=ubf00372a-92b6-4&from=paste&id=u167c1c77&originHeight=393&originWidth=944&originalType=url&ratio=1&status=done&style=none&taskId=uecc46b4f-b316-424f-8ada-388b05039b9)
这里看到大致清楚了，是获取应用签名信息，也就是签名校验了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986579-244aeb45-6870-4491-8a94-07317f60fe27.png#clientId=ubf00372a-92b6-4&from=paste&id=u981e8530&originHeight=578&originWidth=871&originalType=url&ratio=1&status=done&style=none&taskId=u9272313b-8a20-4cfe-8759-49d3a1ba9ca)
好吧，在这一处判断exit函数中，应该是通过反射获取Java层的context值，然后在获取应用的签名信息和已经保存的原始某音签名信息做对比，如果不对就退出了。那么过签名校验也很方便，直接利用我之前的kstools原理，把hook代码代码拷贝到工程中，拦截获取签名信息，然后返回正确的签名信息即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986606-6b80a9f1-e856-4f66-a4e5-ed79b01bf780.png#clientId=ubf00372a-92b6-4&from=paste&id=u29040107&originHeight=182&originWidth=645&originalType=url&ratio=1&status=done&style=none&taskId=u96c114b7-cb39-463c-9b3f-fe78147d507)
具体的hook代码去看我的kstools实现原理即可：[Android中自动爆破签名信息工具kstools](http://www.wjdiankong.cn/android%E4%B8%AD%E5%B8%A6%E4%BD%A0%E5%BC%80%E5%8F%91%E4%B8%80%E6%AC%BE%E8%87%AA%E5%8A%A8%E7%88%86%E7%A0%B4%E7%AD%BE%E5%90%8D%E6%A0%A1%E9%AA%8C%E5%B7%A5%E5%85%B7kstools/)；然后hook代码一定要在Application的第一行代码调用，不然没有效果的。这样操作完成之后，其实已经出数据了，不过为了能够进入加密函数进行调试分析具体的加密算法，我们继续调试JNI_OnLoad函数：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986627-d7912d4f-f56b-489e-a6c3-85dee088fb45.png#clientId=ubf00372a-92b6-4&from=paste&id=u60a93a1a&originHeight=251&originWidth=816&originalType=url&ratio=1&status=done&style=none&taskId=u8a544af4-8137-410b-8785-df4e5aeccde)
继续往下走，会发现在第五个exit判断之后，有一个函数出问题了，就是这个BL，进入内部查看：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986983-55ce8cfb-44d8-4f4e-8d73-ae7513f96424.png#clientId=ubf00372a-92b6-4&from=paste&id=u6326ab5c&originHeight=580&originWidth=837&originalType=url&ratio=1&status=done&style=none&taskId=u5390f066-0495-478c-8e48-9911523e4e0)
哈哈，发现了这个字符串信息，弄过调试的同学大致都猜到了，这个是反调试操作，继续往下走：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609986979-a22efd7f-4665-42cb-a8e1-e9717fdc2ad4.png#clientId=ubf00372a-92b6-4&from=paste&id=u15c53f75&originHeight=461&originWidth=950&originalType=url&ratio=1&status=done&style=none&taskId=u9209e86b-c13f-4abc-a8d3-599ad6d59d0)
这里可以百分百确定是读取status文件中的TracerPid字段值来进行反调试检测了，给下面的两个CMP指令下个断点：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987123-7b2fe150-6fd9-4faa-8e5b-5d241de848bd.png#clientId=ubf00372a-92b6-4&from=paste&id=u817790db&originHeight=428&originWidth=817&originalType=url&ratio=1&status=done&style=none&taskId=u6f030279-4e55-4ad6-aade-1a684c435b7)
发现R3寄存器中的确不是0，所以为了过了反调试，直接修改R3寄存器值为0即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987233-7b081c35-593f-457e-9bb0-03d3e980790a.png#clientId=ubf00372a-92b6-4&from=paste&id=u02a333b8&originHeight=373&originWidth=851&originalType=url&ratio=1&status=done&style=none&taskId=u37f5d9a5-136c-491d-b9ef-cc35cb5d6d6)
这样就过了反调试检测了，单步往下走到下一个exit的判断断点，最终还有一个地方需要处理就是第七个CMP指令：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987262-c4f31f32-c9ad-407a-9b1b-768fb11c3785.png#clientId=ubf00372a-92b6-4&from=paste&id=u8bdef3f1&originHeight=188&originWidth=746&originalType=url&ratio=1&status=done&style=none&taskId=u984753be-1be3-4927-ad53-a323c78ae47)
处理方法直接修改R3寄存器中的值为0即可，就这样我们成功的过了JNI_OnLoad中的所有判断exit的地方了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987412-ec0fdfd9-df90-4d50-976d-101b7b6cf61b.png#clientId=ubf00372a-92b6-4&from=paste&id=u7828c34b&originHeight=553&originWidth=673&originalType=url&ratio=1&status=done&style=none&taskId=udbbd9599-9736-4da8-9638-86cfdc0ffa7)
然后给加密函数getUserInfo下个断点：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987441-8121ed43-4499-4ab6-b2dc-49beeaea2845.png#clientId=ubf00372a-92b6-4&from=paste&id=u70cac568&originHeight=144&originWidth=669&originalType=url&ratio=1&status=done&style=none&taskId=uc088a05a-0981-4367-ab25-b27045f8611)
直接点击进入即可：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987641-0d1dfcf4-2ce0-4480-8b81-9e718a1838e1.png#clientId=ubf00372a-92b6-4&from=paste&id=u612e4d27&originHeight=550&originWidth=756&originalType=url&ratio=1&status=done&style=none&taskId=ua1dd616c-53fa-46d2-aae8-21d93e83f82)
​


# **四、加密结果输出**
也成功到达了加密函数断点处，这里已经没有任何判断逻辑了，就是一个单纯的加密函数了，不过这里不在进行调试分析了，感兴趣的同学可以自己操作了，因为我们的目的达到了，就是成功的获取到了加密之后的数据了：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987736-7a33ee5e-4b13-4f4a-9d25-134928c80fbc.png#clientId=ubf00372a-92b6-4&from=paste&id=u44869767&originHeight=589&originWidth=830&originalType=url&ratio=1&status=done&style=none&taskId=u409e030e-7659-4764-bfbe-a30daaff1aa)
看到了，我们成功的获取到了as和cp值，然后构造到请求url中，也成功的拿到了返回数据。我们这里多了一步解析json数据而已，原始的json数据是这样的：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987869-0bfd748a-dd3f-4643-ba63-5209750f8b34.png#clientId=ubf00372a-92b6-4&from=paste&id=u75ac9b64&originHeight=649&originWidth=821&originalType=url&ratio=1&status=done&style=none&taskId=ub7a26210-e731-4226-89af-4339b94f3a3)
之所以要解析，也是为了后面的项目准备的，到时候我会公开项目的开发进程。不管怎么样，到这里我们就成功的获取某音的加密信息了。主要通过动态调试JNI_OnLoad函数来解决so调用闪退问题，在文章开头的时候说到了，其实本文可以直接用简单粗暴的方式解决，就是万能大法：**全局搜索字符串信息**，这里包括Jadx中查找和IDA中查看，因为字符串信息能给我们带来的信息非常多，有时候靠猜一下就可以定位到破解口了，比如这里，我们在IDA中使用快捷键Shift+F12打开字符串窗口：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987882-315e066a-aec1-47ec-b647-3bf38b0d0761.png#clientId=ubf00372a-92b6-4&from=paste&id=u44500601&originHeight=491&originWidth=1183&originalType=url&ratio=1&status=done&style=none&taskId=u98b6c354-589e-475f-8081-4942101c20a)
凭着这些关键字符串信息就能断定so中做了哪些操作。记住这些敏感的字符串信息，对日后的逆向非常关键。
​


# **五、技术总结**
上面就解决了某音的请求数据加密信息问题了，下面来总结一下本次逆向学习到的技术：

- 第一、看到在native层用反射去调用Java层的方法获取信息也是一种防护so被恶意调用的方式。比如本文的context变量获取。
- 第二、签名校验永远都不过时，其实本文当时没想到他有签名校验，因为看到native函数中都没有传递context变量，谁知道他是用反射调用Java层方法获取的，长知识和经验了。
- 第三、反调试也是永远不过时的，不过他这里的反调试检测有点简单了，就一处而且就一个进程，如果高级点应该启多个进程，循环检查tracepid值进行校验，会增大难度。
- 第四、在逆向中有时候在Java层没必要去花时间分析一个方法的参数和返回值构造情况，直接利用Xposed进行hook大法打印方法的参数信息靠猜也就出来了。
- 第五、静态方式分析永远都不会过时，全局搜索字符串也是最基本法则，靠猜就可以快速获取结果。

# **六、某山小视频加密分析**
上面解决了某音的加密问题，下面再来看一下某山小视频的加密信息，突破口依然是使用Fiddler进行抓包查看数据：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609987889-282da18c-6453-4252-8722-dc3b0177100a.png#clientId=ubf00372a-92b6-4&from=paste&id=u21908ed6&originHeight=789&originWidth=997&originalType=url&ratio=1&status=done&style=none&taskId=u0fc3954f-bb2f-485d-8338-e9ab560a5b4)
通过抓包会很神奇的发现，和某音的数据结构字段几乎异曲同工，果然是自家兄弟，继续查看他的加密字段：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609988164-9db1c873-4d70-4076-8f0a-378c07aba35e.png#clientId=ubf00372a-92b6-4&from=paste&id=ub8c8385f&originHeight=371&originWidth=350&originalType=url&ratio=1&status=done&style=none&taskId=u48f78337-6b30-4627-b8bc-d563514245d)
不想多说了，既然加密的字段都是一样的。那么我们直接不多说用Jadx打开某山小视频，看看他有没有那个native类UserInfo信息：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609988282-5cbd07bf-0754-4abf-85b7-4865ff137438.png#clientId=ubf00372a-92b6-4&from=paste&id=ud717e48d&originHeight=309&originWidth=758&originalType=url&ratio=1&status=done&style=none&taskId=u668fda3f-b5a4-4042-a9e9-9ddf7b0a6eb)
哈哈，看来不用多一次分析了，完全一样，那么直接用同一个so，同一个加密即可，在demo工程中运行查看日志：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609988665-82364c9d-b7aa-4565-a43e-e26f2124d1cf.png#clientId=ubf00372a-92b6-4&from=paste&id=u98f126df&originHeight=249&originWidth=907&originalType=url&ratio=1&status=done&style=none&taskId=u38ff5faa-2158-42d8-9a80-910ea9379a9)
初始化都是一样的，直接运行：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609988660-47bfde7f-a664-4bd6-b7fb-3ec062768fd8.png#clientId=ubf00372a-92b6-4&from=paste&id=u22f0b9e8&originHeight=376&originWidth=708&originalType=url&ratio=1&status=done&style=none&taskId=u2dec379c-8f09-43d5-97f6-c93432ed3dc)
看到了，数据也回来了，看看他的原始json数据：
![](https://cdn.nlark.com/yuque/0/2021/png/97322/1627609988711-ff5c0744-d974-4c23-a4ad-18277ae01585.png#clientId=ubf00372a-92b6-4&from=paste&id=u0c842c96&originHeight=768&originWidth=882&originalType=url&ratio=1&status=done&style=none&taskId=ub7149b90-88ea-4011-afc2-77574b9c295)
到这里，我们就成功的破解了某音和某山小视频的数据请求协议了，有了这两个短视频数据，后面我们开发app就简单了，当然在文章开始的时候也说了，现阶段短视频四小龙：某音，某山，某拍，某手，那么已经干掉了前面两个，下一个是谁呢？猜对有奖。争取在年底把这四个app全部爆破成功，能够请求到他的数据。为我们明年的app作为基础
**严重声明**
**本文的意图只有一个就是通过分析app学习更多的逆向技术，如果有人利用本文知识和技术进行非法操作进行牟利，带来的任何法律责任都将由操作者本人承担，和本文作者无任何关系，最终还是希望大家能够秉着学习的心态阅读此文。鉴于安全问题，样本和源码都去编码美丽小密圈自取！**

# **七、总结**
通过本文可以发现，我们其实没有真正意义上的破解它的算法，但是结果却是我们想要的，这就够了。而对于现在很多app把加密算法放到so中，在对so做一些防护，这样就很难利用本文的技术去调用app的so了。不过so再怎么防护就是那么几种方法，我们依然可以用动态调试来解决。有人在文中很好奇，那些判断校验不能直接修改so指令做到吗？比如签名校验，没必要在Java层进hook呀，直接修改CMP和BEQ指令呗？
​


————————————————
> 附上：
> [抖音数据采集SDK](https://github.com/Video-Hub/douyin-sdk)

