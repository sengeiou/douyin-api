# 抖音数据采集教程，unidbg Ollvm 字符串加密解析，抖音数据爬取




原理： 利用 unicorn 的 mem hook 在内存加载完 so 后，dump 出纯净的数据。
有两种方法：

## 核心方法

- 方法1: 利用 `getBackend()` 来复刻 Unicorn 的内存 hook 方法。
- 方法2: 利用 `traceCode()` 来 trace 指定内存段， hook 内存数据。

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

注意，`getBackend` 是用 `Unicorn` 来驱动的， 所以只能用在 dbg 的 unicorn 引擎上
以 `getBackend` 为例
```
emulator.getBackend().hook_add_new(new WriteHook() {
    @Override
    public void hook(Backend backend, long address, int size, long value, Object user) {
        System.out.println(Long.toHexString(address) + "----" + size + "----" + value);
        byte[] bytes = long2Bytes(value, size);
        modifyMap.put(address, bytes);
    }
}, 0, Long.MAX_VALUE, null);
```
**Java**
_ 复制_
这样就可以 hook 在指定时刻hook内存数据了。

# 衍生

 
当然可以加以封装一个 GUI 这样就可以鼠标点点就可以完成工作了
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612091862864-b1751ecd-f587-4e78-ae8f-8ab660102772.jpeg#align=left&display=inline&height=400&originHeight=400&originWidth=511&size=0&status=done&style=none&width=511)](https://static.zhangkunzhi.com/2021/01/03/16096794798438.jpg?x-oss-process=image/resize,h_400)
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612091862857-f0cde7cd-b5ba-4324-bb4b-4bb3532a9b5e.jpeg#align=left&display=inline&height=400&originHeight=400&originWidth=608&size=0&status=done&style=none&width=608)](https://static.zhangkunzhi.com/2021/01/03/16096795102653.jpg?x-oss-process=image/resize,h_400)
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612091862877-ede442bb-1e96-49d5-a787-046df46f78e2.jpeg#align=left&display=inline&height=108&originHeight=108&originWidth=543&size=0&status=done&style=none&width=543)](https://static.zhangkunzhi.com/2021/01/03/16096785041804.jpg?x-oss-process=image/resize,h_400)
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612091862863-e6a5f816-1c2a-4ba0-ae69-1d5ee15bf2af.jpeg#align=left&display=inline&height=110&originHeight=110&originWidth=705&size=0&status=done&style=none&width=705)](https://static.zhangkunzhi.com/2021/01/03/16096785409608.jpg?x-oss-process=image/resize,h_400)


 

