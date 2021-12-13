# 抖音数据采集教程，unicorn 解决 OLLVM 字符串混淆通用方法，python爬取抖音视频评论



虽然是通用方法，但是要了解 so 加载流程、汇编基础、java基础才可以使用哦～
> 这里用的基于 unicorn 的 androidEmu

AndroidNativeEmu 在面对没有加壳的 so 还是挺好用的，不过遇到加壳的 so，就会力不从心的，需要 dbg 才行。

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

---

> 解决字符串混淆思路，其实和 frida 读取混淆字符串方法一样，在内存中字符串是被解密状态的，所以在内存中拿就可以了， 而且 `Unicorn` 提供了 `unicorn.UC_HOOK_MEM_WRITE` 来 hook 操作内存中的数据

干货代码如下：
```
import logging
import sys
import os
import unicorn
import struct
from androidemu.emulator import Emulator
sys.path.append(os.path.abspath(os.path.dirname(os.path.dirname(__file__))))
logging.basicConfig(
    stream=sys.stdout,
    level=logging.DEBUG,
    format="%(asctime)s %(levelname)7s %(name)34s | %(message)s"
)
logger = logging.getLogger(__name__)
emulator = Emulator(vfp_inst_set=True)  # 初始化模拟器
# 字符串解密
so_file = "test/test.so"
modify_map = {}  # 接收字典
def UC_HOOK_MEM_WRITE(mu, _type, address, size, _value, user_data):
    byte_array = struct.pack("I", value)[:size]
    modify_map[address] = byte_array
    return
# 加载预设 so
emulator.load_library("example_binaries/libdl.so", do_init=False)
emulator.load_library("example_binaries/libc.so", do_init=False)
emulator.load_library("example_binaries/libstdc++.so", do_init=False)
emulator.load_library("example_binaries/libm.so", do_init=False)
lib_module = emulator.load_library(so_file, do_init=True)  # do_init 需要设置为 true
emulator.mu.hook_add(unicorn.UC_HOOK_MEM_WRITE, UC_HOOK_MEM_WRITE)  # 准备 UC_HOOK_MEM_WRITE 回调解决字符串加密
"""
读出代码，并将被加密的字符串修改回去
"""
with open(so_file, 'rb') as f:
    content = f.read()
print(modify_map)
for i in modify_map:
    value = modify_map[i]
    base = lib_module.base  # 加载基质
    if base <= i <= (base + lib_module.size):  # 利用加载地址判断
        offset = i - base - 0x1000  # 并不是连续加载，是文件中的偏移
        content = content[:offset] + value + content[offset + len(value):]
down_file = so_file + ".fix"
with open(down_file, 'wb') as df:
    df.write(content)
    print('写出完毕', down_file)
```
**
修改 `so_file = so文件地址` 运行后就会导出解密后的 so
注意事项：

- 有的时候so有交叉调用，无法直接解密，需要hook住调用函数，来解决异常
