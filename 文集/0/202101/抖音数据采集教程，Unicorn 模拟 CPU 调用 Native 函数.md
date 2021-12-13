# 抖音数据采集教程，Unicorn 模拟 CPU 调用 Native 函数，python爬取抖音视频


# 接口
**内存相关**

- uc_mem_map
- uc_mem_read
- uc_mem_write

**寄存器相关**

- uc_reg_read
- uc_reg_write

**指令执行类**

- UC_HOOK_INTR
- UC_HOOK_INSN
- UC_HOOK_CODE
- UC_HOOK_BLOCK

**内存访问类**

- UC_HOOK_MEM_READ
- UC_HOOK_MEM_WRITE
- UC_HOOK_MEM_FETCH
- .....

**异常处理类**

- UC_HOOK_MEM_READ_UNMAPPED
- UC_HOOK_MEM_WRITE_UNMAPPED
- UC_HOOK_MEM_FETCH_UNMAPPED

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

# Unicorn 上手
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663945-dc2ca416-5477-430d-801c-ac044894c679.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=463&size=0&status=done&style=none&width=463)](https://static.zhangkunzhi.com/2020/12/08/16070694429335.jpg?x-oss-process=image/resize,h_400)<br>先自己写个 超级简单的app，并编译（没有开混淆保护）
```
#include <jni.h>
#include <string>
int add(int a, int b){
    int sum=a+b;
    return sum;
}
int add_six(int a,int b,int c, int d, int e,int f){
    int sum=0;
    sum=add(a,b);
    sum=add(sum,c);
    sum=add(sum,d);
    sum=add(sum,e);
    sum=add(sum,f);
    return sum;
}
extern "C" JNIEXPORT jstring JNICALL
Java_com_zok_uni_MainActivity_stringFromJNI(
        JNIEnv* env,
        jobject /* this */) {
    std::string hello = "Hello from C++";
    int sum=add(3,4);
    sum=add_six(1,2,3,4,5,6);
    return env->NewStringUTF(hello.c_str());
}
```
**C**<br>_ 复制_<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663808-2c3a000c-d808-434c-8444-add150d97b5c.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=379&size=0&status=done&style=none&width=379)](https://static.zhangkunzhi.com/2020/12/08/16072633110515.jpg?x-oss-process=image/resize,h_400)<br>抹去符号，让他直接在 so 中调用 `add_definitions(-fvisibility=hidden)`<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663800-6a4ac238-029b-4ba5-aa11-621f7da16697.jpeg#align=left&display=inline&height=224&margin=%5Bobject%20Object%5D&originHeight=224&originWidth=676&size=0&status=done&style=none&width=676)](https://static.zhangkunzhi.com/2020/12/08/16072633338056.jpg?x-oss-process=image/resize,h_400)<br>**32位下，参数小于4个的时候会直接通过寄存器来传递！！多余的只能进入堆栈当中（从右到左依次入栈）**

# IDA 分析
打开IDA 查看位置，为了查看方便我们配置一下， `Options--general`<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663770-074fe20e-e8be-477a-ac82-dfb220944c7b.jpeg#align=left&display=inline&height=241&margin=%5Bobject%20Object%5D&originHeight=241&originWidth=544&size=0&status=done&style=none&width=544)](https://static.zhangkunzhi.com/2020/12/08/16070731657275.jpg?x-oss-process=image/resize,h_400)<br>简单的找到函数位置, 并改名<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663732-822b6a2a-4fb4-4cb4-8eb1-697c4a064313.jpeg#align=left&display=inline&height=228&margin=%5Bobject%20Object%5D&originHeight=228&originWidth=476&size=0&status=done&style=none&width=476)](https://static.zhangkunzhi.com/2020/12/08/16072638247087.jpg?x-oss-process=image/resize,h_400)<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663690-6f8b379f-a368-4db9-9061-7753cf1cafed.jpeg#align=left&display=inline&height=324&margin=%5Bobject%20Object%5D&originHeight=324&originWidth=633&size=0&status=done&style=none&width=633)](https://static.zhangkunzhi.com/2020/12/08/16073211716894.jpg?x-oss-process=image/resize,h_400)<br>**动态调试**

1. 我们动态 IDA 动态调试看看，手机启动 android 服务。并新启动一个 IDA 附加进程。打开 Modules 模块（Debugger--DebuggerWindows--ModulesList）
1. Modules 中搜索[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833664776-ff0902bb-c017-45c1-b938-406d3aa8be9d.jpeg#align=left&display=inline&height=310&margin=%5Bobject%20Object%5D&originHeight=310&originWidth=795&size=0&status=done&style=none&width=795)](https://static.zhangkunzhi.com/2020/12/08/16073951124788.jpg?x-oss-process=image/resize,h_400)查看 so 的加载[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663851-14bcdf8e-9086-4159-a656-fa600e9215b3.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=776&size=0&status=done&style=none&width=776)](https://static.zhangkunzhi.com/2020/12/08/16073951518047.jpg?x-oss-process=image/resize,h_400)<br>找到 `stringFromJNI` 的真实绑定地址就是在 `C78CD628` 中

**接下来就要看他参数的调用传递情况和堆栈情况**

1. `F9` 调试运行<br>对照静态ida[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663848-dfda6fc1-fc0c-4654-9357-df76f61f827d.jpeg#align=left&display=inline&height=85&margin=%5Bobject%20Object%5D&originHeight=85&originWidth=592&size=0&status=done&style=none&width=592)](https://static.zhangkunzhi.com/2020/12/08/16073959579299.jpg?x-oss-process=image/resize,h_400)

8646和8648 下面就是 864A 也就是 add 函数了[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663867-75653668-7f50-4909-af8e-95cbd52bf9ad.jpeg#align=left&display=inline&height=98&margin=%5Bobject%20Object%5D&originHeight=98&originWidth=401&size=0&status=done&style=none&width=401)](https://static.zhangkunzhi.com/2020/12/08/16073960189836.jpg?x-oss-process=image/resize,h_400)<br>所以此处对应 add 函数我们可以改名， 同理可以找到 add_six 函数

# 结论
继续调试后总结如下：

1. **ARM32位下，参数小于4个的时候会直接通过寄存器来传递(R0-R3)！从右至左依次入！多余的只能进入堆栈当中（从右到左依次入栈）**
1. **ARM64位下，参数小于8个会通过 (X0-X7)寄存器传递从右至左依次入栈；多余的要使用栈来传递剩余参数**
1. **32位下，THUMB 指令集是满栈模式，sp 始终指向有值的内容**

# 实际操作
> 先记住两个函数的偏移位置

[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663861-ad4e8563-0a5b-422c-86fe-297ba9ae6e2d.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=1097&size=0&status=done&style=none&width=1097)](https://static.zhangkunzhi.com/2020/12/08/16073992942008.jpg?x-oss-process=image/resize,h_400)<br>add 函数偏移位置 8500<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663846-7ba0f050-1a67-4bdd-943b-99b8b8586b5c.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=911&size=0&status=done&style=none&width=911)](https://static.zhangkunzhi.com/2020/12/08/16073994738567.jpg?x-oss-process=image/resize,h_400)<br>add_six 函数偏移位置 851C<br>接下来就可以通过函数传递和堆栈布局来对这两个函数的模拟调用
```
"""
实现对 so 中函数调用
示例apk中两个函数
int add(int a, int b){
    int sum=a+b;
    return sum;
}
int add_six(int a,int b,int c, int d, int e,int f){
    int sum=0;
    sum=add(a,b);
    sum=add(sum,c);
    sum=add(sum,d);
    sum=add(sum,e);
    sum=add(sum,f);
    return sum;
}
extern "C" JNIEXPORT jstring JNICALL
Java_com_zok_uni_MainActivity_stringFromJNI(
        JNIEnv* env,
        jobject /* this */) {
    std::string hello = "Hello from C++";
    int sum=add(3,4);
    sum=add_six(1,2,3,4,5,6);
    return env->NewStringUTF(hello.c_str());
}
"""
import unicorn
import capstone
import binascii
import struct
# 取出 so 内容
with open("so/03.so",'rb') as f:
    CODE=f.read() 
def capstone_print(code, offset):
    """capstone 测试输出"""
    print("\033[1;32m-------- capstone 输出--------\033[0m")
    CP = capstone.Cs(capstone.CS_ARCH_ARM, capstone.CS_MODE_THUMB)  # 指定 THUMB 指令集
    for i in CP.disasm(code[offset:], 0, 20):  
        print('\033[1;32m地址: 0x%x | 操作码: %s | 内容: %s\033[0m'%(offset + i.address, i.mnemonic, i.op_str))
def uni_add():
    """
    add(a+b)
    将汇编片段，映射到 unicorn 虚拟内存中，将 pc 指向第一条指令处并执行
    """
    print('-------- unicorn 执行前--------')
    # 1. 创建实例
    mu = unicorn.Uc(unicorn.UC_ARCH_ARM, unicorn.UC_MODE_THUMB)  # 要指定架构和模式, 这里用 arm 架构， 指定 THUMB 指令集
    # 2. 将代码片段映射到模拟器的虚拟地址
    ADDRESS = 0x1000 # 映射开始地址
    SIZE = 1024*1024*10  # 分配映射大小(多分一点)
    # 3. 开始映射
    mu.mem_map(ADDRESS, SIZE)  # 初始化映射 参数1：地址 参数2:空间大小  默认初始化后默认值：0
    mu.mem_write(ADDRESS, CODE)  # 写入指令 参数1: 写入位置 参数2:写入内容
    # 写入寄存器
    # 4. 寄存器初始化 指令集涉及到 R0，R1，R2，R3 4个寄存器
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R0, 0x1)  # 在 r0 寄存器上写入 0x1
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R1, 0x2)  # 在 r1 寄存器上写入 0x2
    # 5. 初始化堆栈，因为要对内存进行操作 设置 SP
    SP = ADDRESS+SIZE-1
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_SP,SP)
    # 6. pc 指针指向地址开始执行 Hook (暂时屏蔽)
    # mu.hook_add(unicorn.UC_HOOK_CODE, hook_code)  # 跟踪 cpu 执行状态 hook 这里默认跟踪所有，具体也可以配置
    # mu.hook_add(unicorn.UC_HOOK_MEM_WRITE, hook_mem)  # 跟踪 cpu 执行内存操作， 需要自写回调函数
    # mu.hook_add(unicorn.UC_HOOK_INTR,hook_syscall)  # hook 系统调用函数
    # mu.hook_add(unicorn.UC_HOOK_BLOCK,hook_block)  # hook 基本块   
    # 因为有内存操作,hook的时候并没有映射内存，就会报错。所以要～主动映射内存 
    # mu.hook_add(unicorn.UC_HOOK_MEM_WRITE_UNMAPPED,hook_mem_write_unmapped)
    
    print_result(mu)  # capstone 输出
    try: 
        add_satrt = ADDRESS+0x8500+1  # 偏移位置 ida 查看 THUMB 指令集所以要 ADDRESS +1,    
        add_end = ADDRESS+0x851A  # 因为 IDA 中 0x851A 最后一条是 LR，我们这里不需要所以不 +1 即可
        mu.emu_start(add_satrt, add_end)  # 参数1:起始位置，参数2:结束位置
        print('-------- unicorn 执行后--------')
        print_result(mu)  # capstone 输出
    except unicorn.UcError as e:
        print('\033[1;31mError: %s \033[0m' % e)
def uni_add_six():
    """6个参数超过 amr32 4个寄存器，需要将多的2个参数放到堆栈当中"""
    print('-------- unicorn 执行前--------')
    # 1. 创建实例
    mu = unicorn.Uc(unicorn.UC_ARCH_ARM, unicorn.UC_MODE_THUMB)  # 要指定架构和模式, 这里用 arm 架构， 指定 THUMB 指令集
    # 2. 将代码片段映射到模拟器的虚拟地址
    ADDRESS = 0x1000 # 映射开始地址
    SIZE = 1024*1024*10  # 分配映射大小(多分一点)
    # 3. 开始映射
    mu.mem_map(ADDRESS, SIZE)  # 初始化映射 参数1：地址 参数2:空间大小  默认初始化后默认值：0
    mu.mem_write(ADDRESS, CODE)  # 写入指令 参数1: 写入位置 参数2:写入内容
    # 写入寄存器
    # 4. 寄存器初始化 指令集涉及到 R0，R1，R2，R3 4个寄存器
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R0, 0x1)  # 在 r0 寄存器上写入 0x1
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R1, 0x2)  # 在 r1 寄存器上写入 0x2
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R2, 0x3)  # 在 r1 寄存器上写入 0x3
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R3, 0x4)  # 在 r1 寄存器上写入 0x4
    # 但是 IDA 中我们并没有做堆栈平衡处理，要指向一个地址，他才能执行完
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_LR,ADDRESS+0x456)  # 随便指向 0x456 一个存在的地址
    # 5. 初始化堆栈，因为要对内存进行操作 设置 SP
    SP = ADDRESS+SIZE-16  # 多减点，预留 sp 剩下两个参数的位置
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_SP,SP)
    # 6. 多的两个参数，5和 6 要手动放入堆栈当中(从右至左)
    mu.mem_write(SP, struct.pack('I', 5))
    mu.mem_write(SP+4, struct.pack('I', 6))
    
    print_result(mu)  # capstone 输出
    try: 
        add_satrt = ADDRESS+0x851C+1  # 偏移位置 ida 查看 THUMB 指令集所以要 ADDRESS +1,    
        add_end = ADDRESS+0x858E + 8 # 因为我们手动平衡了内存所以多给点空间
        mu.emu_start(add_satrt, add_end)  # 参数1:起始位置，参数2:结束位置
        print('-------- unicorn 执行后--------')
        print_result(mu)  # capstone 输出
    except unicorn.UcError as e:
        print('\033[1;31mError: %s \033[0m' % e)
def print_result(mu):
    """调试寄存器值
    UC_ARM_REG_R0 = 66
    UC_ARM_REG_R1 = 67
    UC_ARM_REG_R2 = 68
    UC_ARM_REG_R3 = 69
    UC_ARM_REG_R4 = 70
    """
    for i in range(66,78):
        print("寄存器[R%d], hex 值:%x"%(i-66,mu.reg_read(i)))
    print("SP 值:%x" % (mu.reg_read(unicorn.arm_const.UC_ARM_REG_SP)))
    print("PC 值:%x" % (mu.reg_read(unicorn.arm_const.UC_ARM_REG_PC)))
if __name__ == "__main__":
    # add 2 参数的相加
    print('--------------add --------------')
    capstone_print(CODE, 0x8500)
    uni_add()  # 模拟 add 函数
    print('\n-------------- add_six --------------')
    # add_six 6个参数的相加
    capstone_print(CODE, 0x851C)
    uni_add_six()
```
**Python**<br>_ 复制_

# 调用 libc 函数
**依赖调用了其他so函数的情况下就不能像之前例子这样调用了**<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833664845-f8ee18d6-a97a-49bf-a88b-fc377e80acbe.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=1200&size=0&status=done&style=none&width=1200)](https://static.zhangkunzhi.com/2020/12/08/16074105898959.jpg?x-oss-process=image/resize,h_400)<br>c 中函数如图， 我们就需要在调用了外部函数的位置打 排齐<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663776-db0d8512-c528-4228-b298-32aee01ba51f.jpeg#align=left&display=inline&height=356&margin=%5Bobject%20Object%5D&originHeight=356&originWidth=778&size=0&status=done&style=none&width=778)](https://static.zhangkunzhi.com/2020/12/08/16074110734371.jpg?x-oss-process=image/resize,h_400)<br>调用位置在 `859A`
```
"""
【依赖调用了其他so函数的情况下就不能像之前例子这样调用了】
实现对 so 中函数调用
int add_six(char* flag,int b,int c, int d, int e,int f){
    int sum=0;
    if(strstr(flag, "add")){
        sum=add(sum,c);
        sum=add(sum,d);
    }else{
        sum=add(sum,e);
        sum=add(sum,f);
    }
}
extern "C" JNIEXPORT jstring JNICALL
Java_com_zok_uni_MainActivity_stringFromJNI(
        JNIEnv* env,
        jobject /* this */) {
    std::string hello = "Hello from C++";
    int sum=add(3,4);
    sum=add_six("flag",2,3,4,5,6);
    return env->NewStringUTF(hello.c_str());
}
"""
import unicorn
import capstone
import binascii
import struct
# 取出 so 内容
with open("so/callstrstr.so",'rb') as f:
    CODE=f.read() 
def capstone_print(code, offset):
    """capstone 测试输出"""
    print("\033[1;32m-------- capstone 输出--------\033[0m")
    CP = capstone.Cs(capstone.CS_ARCH_ARM, capstone.CS_MODE_THUMB)  # 指定 THUMB 指令集
    for i in CP.disasm(code[offset:], 0, 20):  
        print('\033[1;32m地址: 0x%x | 操作码: %s | 内容: %s\033[0m'%(offset + i.address, i.mnemonic, i.op_str))
def uni_add_six():
    """6个参数超过 amr32 4个寄存器，需要将多的2个参数放到堆栈当中"""
    print('-------- unicorn 执行前--------')
    # 1. 创建实例
    mu = unicorn.Uc(unicorn.UC_ARCH_ARM, unicorn.UC_MODE_THUMB)  # 要指定架构和模式, 这里用 arm 架构， 指定 THUMB 指令集
    # 2. 将代码片段映射到模拟器的虚拟地址
    ADDRESS = 0x1000 # 映射开始地址
    SIZE = 1024*1024*10  # 分配映射大小(多分一点)
    # 3. 开始映射
    mu.mem_map(ADDRESS, SIZE)  # 初始化映射 参数1：地址 参数2:空间大小  默认初始化后默认值：0
    mu.mem_write(ADDRESS, CODE)  # 写入指令 参数1: 写入位置 参数2:写入内容
    """处理外部 so 调用"""
    # 此处要给，调用了外部 so 的地址写入 nop， 然后通过添加回调函数来实现效果
    mu.mem_write(ADDRESS+0x859A, b'\x00\xbf\x00\xbf')  # \x00\xbf\x00\xbf 为 两个 nop， 因为0x859A处有4个字节，所以用两个nop 填充
    # 写入寄存器
    # 4. 寄存器初始化 指令集涉及到 R0，R1，R2，R3 4个寄存器
    # 第一个参数是 string ，需要给指针、
    mu.mem_map(ADDRESS+SIZE+0x1000, 1024)  # 开辟
    mu.mem_write(ADDRESS+SIZE+0x1000, b'flag2')  # 写入
    bytes=mu.mem_read(ADDRESS+SIZE+0x1000,5)  # 调试输出
    print(binascii.b2a_hex(bytes))
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R0, ADDRESS+SIZE+0x1000)  # 在 r0 寄存器上写入刚刚创建的指针
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R1, 0x2)  # 在 r1 寄存器上写入 0x2
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R2, 0x3)  # 在 r1 寄存器上写入 0x3
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_R3, 0x4)  # 在 r1 寄存器上写入 0x4
    # 但是 IDA 中我们并没有做堆栈平衡处理，要指向一个地址，他才能执行完
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_LR,ADDRESS+0x456)  # 随便指向 0x456 一个存在的地址
    # 5. 初始化堆栈，因为要对内存进行操作 设置 SP
    SP = ADDRESS+SIZE-16  # 多减点，预留 sp 剩下两个参数的位置
    mu.reg_write(unicorn.arm_const.UC_ARM_REG_SP,SP)
    # 6. 多的两个参数，5和 6 要手动放入堆栈当中(从右至左)
    mu.mem_write(SP, struct.pack('I', 5))
    mu.mem_write(SP+4, struct.pack('I', 6))
    # hook 代码
    mu.hook_add(unicorn.UC_HOOK_CODE, hook_code)
    mu.hook_add(unicorn.UC_HOOK_INTR,hook_syscall)  # hook 系统调用函数
    mu.hook_add(unicorn.UC_HOOK_BLOCK,hook_block)  # hook 基本块   
    print_result(mu)  # capstone 输出
    try: 
        add_satrt = ADDRESS+0x854C+1  # 偏移位置 ida 查看 THUMB 指令集所以要 ADDRESS +1,    
        add_end = ADDRESS+0x85D8 # 因为我们手动平衡了内存所以多给点空间
        mu.emu_start(add_satrt, add_end)  # 参数1:起始位置，参数2:结束位置
        print('-------- unicorn 执行后--------')
        print_result(mu)  # capstone 输出
    except unicorn.UcError as e:
        print('\033[1;31mError: %s \033[0m' % e)
def hook_code(mu, address, size, user_data):
    """定义回调函数， 在进入汇编指令之前就会先运行这里
    mu: 模拟器
    address: 执行地址
    size: 汇编指令大小
    user_data: 通过 hook_add 添加的参数
    """
    code=mu.mem_read(address,size)  # 读取
    if address==0x1000+0x859A:  # 外部 so 调用地址
        """hook 两个参数并返回正确值（自行计算）"""
        r0value=readstring(mu,mu.reg_read(unicorn.arm_const.UC_ARM_REG_R0))
        r1value = readstring(mu, mu.reg_read(unicorn.arm_const.UC_ARM_REG_R1))
        index=r0value.find(r1value)  # 用 find 的方法模拟实现并写入 R0 寄存器中即可
        if index==-1:  # 没有找到的话，就返回 0 
            mu.reg_write(unicorn.arm_const.UC_ARM_REG_R0,0)
        else:  # 找到的话，就返回位置
            mu.reg_write(unicorn.arm_const.UC_ARM_REG_R0, index)
        print("\033[1;36m执行外部 so 函数 strstr 参数1: %s, 参数2: %s\033[0m"%(r0value, r1value))
    CP = capstone.Cs(capstone.CS_ARCH_ARM, capstone.CS_MODE_THUMB)  # 指定 THUMB 指令集
    for i in CP.disasm(code, 0, len(code)):
        print('\033[1;30m【Hook cpu】 地址: 0x%x | 操作码: %s | 内容: %s\033[0m'%(address + i.address, i.mnemonic, i.op_str))
    return 
def hook_syscall(mu,intno,user_data):
    print("\033[1;36mhook 系统调用 系统调用号: 0x%d"%intno)
    if intno==2:  # 例子 2 是退出
        print("系统调用退出!!")
    print_result(mu)
    print("\033[0m")
    return
def hook_block(mu, address, size, user_data):
    # code = mu.mem_read(address,size)
    print("\033[1;36mhook 基本块")
    print_result(mu)
    print("\033[0m")
    return
def print_result(mu):
    """调试寄存器值
    """
    for i in range(66,78):
        print("寄存器[R%d], hex 值:%x"%(i-66,mu.reg_read(i)))
    print("SP 值:%x" % (mu.reg_read(unicorn.arm_const.UC_ARM_REG_SP)))
    print("PC 值:%x" % (mu.reg_read(unicorn.arm_const.UC_ARM_REG_PC)))
def readstring(mu,address):
    """读出结果"""
    result=''
    tmp=mu.mem_read(address,1)
    while(tmp[0]!=0):
        result=result+chr(tmp[0])
        address=address+1
        tmp = mu.mem_read(address, 1)
    return result
if __name__ == "__main__":
    print('\n-------------- add_six 延展外部 so 调用示例--------------')
    capstone_print(CODE, 0x851C)
    uni_add_six()
```
**Python**<br>_ 复制_<br>[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1611833663842-e9b536e6-51b9-455a-927c-26dd126f9826.jpeg#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=272&size=0&status=done&style=none&width=272)](https://static.zhangkunzhi.com/2020/12/08/16074135873049.jpg?x-oss-process=image/resize,h_400)<br>完成模拟操作
```
-------------- add_six 延展外部 so 调用示例--------------
-------- capstone 输出--------
地址: 0x851c | 操作码: movs | 内容: r0, r0
地址: 0x851e | 操作码: b | 内容: #0x124
地址: 0x8520 | 操作码: movs | 内容: r0, #2
地址: 0x8522 | 操作码: b | 内容: #0x128
-------- unicorn 执行前--------
b'666c616732'
寄存器[R0], hex 值:a02000
寄存器[R1], hex 值:2
寄存器[R2], hex 值:3
寄存器[R3], hex 值:4
寄存器[R4], hex 值:0
寄存器[R5], hex 值:0
寄存器[R6], hex 值:0
寄存器[R7], hex 值:0
寄存器[R8], hex 值:0
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00ff0
PC 值:0
hook 基本块
寄存器[R0], hex 值:a02000
寄存器[R1], hex 值:2
寄存器[R2], hex 值:3
寄存器[R3], hex 值:4
寄存器[R4], hex 值:0
寄存器[R5], hex 值:0
寄存器[R6], hex 值:0
寄存器[R7], hex 值:0
寄存器[R8], hex 值:0
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00ff0
PC 值:954c
【Hook cpu】 地址: 0x954c | 操作码: push | 内容: {r4, r5, r6, r7, lr}
【Hook cpu】 地址: 0x954e | 操作码: add | 内容: r7, sp, #0xc
【Hook cpu】 地址: 0x9550 | 操作码: str | 内容: r8, [sp, #-0x4]!
【Hook cpu】 地址: 0x9554 | 操作码: sub | 内容: sp, #0x40
【Hook cpu】 地址: 0x9556 | 操作码: ldr.w | 内容: ip, [r7, #0xc]
【Hook cpu】 地址: 0x955a | 操作码: ldr.w | 内容: lr, [r7, #8]
【Hook cpu】 地址: 0x955e | 操作码: mov | 内容: r4, r3
【Hook cpu】 地址: 0x9560 | 操作码: mov | 内容: r5, r2
【Hook cpu】 地址: 0x9562 | 操作码: mov | 内容: r6, r1
【Hook cpu】 地址: 0x9564 | 操作码: mov | 内容: r8, r0
【Hook cpu】 地址: 0x9566 | 操作码: str | 内容: r0, [sp, #0x2c]
【Hook cpu】 地址: 0x9568 | 操作码: str | 内容: r1, [sp, #0x28]
【Hook cpu】 地址: 0x956a | 操作码: str | 内容: r2, [sp, #0x24]
【Hook cpu】 地址: 0x956c | 操作码: str | 内容: r3, [sp, #0x20]
【Hook cpu】 地址: 0x956e | 操作码: movs | 内容: r0, #0
【Hook cpu】 地址: 0x9570 | 操作码: str | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x9572 | 操作码: ldr | 内容: r0, [sp, #0x2c]
【Hook cpu】 地址: 0x9574 | 操作码: str | 内容: r0, [sp, #0x34]
【Hook cpu】 地址: 0x9576 | 操作码: ldr | 内容: r0, [pc, #0x64]
【Hook cpu】 地址: 0x9578 | 操作码: add | 内容: r0, pc
【Hook cpu】 地址: 0x957a | 操作码: str | 内容: r0, [sp, #0x30]
【Hook cpu】 地址: 0x957c | 操作码: ldr | 内容: r0, [sp, #0x34]
【Hook cpu】 地址: 0x957e | 操作码: ldr | 内容: r1, [sp, #0x30]
【Hook cpu】 地址: 0x9580 | 操作码: str | 内容: r0, [sp, #0x3c]
【Hook cpu】 地址: 0x9582 | 操作码: str | 内容: r1, [sp, #0x38]
【Hook cpu】 地址: 0x9584 | 操作码: ldr | 内容: r0, [sp, #0x3c]
【Hook cpu】 地址: 0x9586 | 操作码: ldr | 内容: r1, [sp, #0x38]
【Hook cpu】 地址: 0x9588 | 操作码: str.w | 内容: ip, [sp, #0x18]
【Hook cpu】 地址: 0x958c | 操作码: str.w | 内容: lr, [sp, #0x14]
【Hook cpu】 地址: 0x9590 | 操作码: str | 内容: r4, [sp, #0x10]
【Hook cpu】 地址: 0x9592 | 操作码: str | 内容: r5, [sp, #0xc]
【Hook cpu】 地址: 0x9594 | 操作码: str | 内容: r6, [sp, #8]
【Hook cpu】 地址: 0x9596 | 操作码: str.w | 内容: r8, [sp, #4]
执行外部 so 函数 strstr 参数1: flag2, 参数2: add
【Hook cpu】 地址: 0x959a | 操作码: nop | 内容: 
【Hook cpu】 地址: 0x959c | 操作码: nop | 内容: 
【Hook cpu】 地址: 0x959e | 操作码: cmp | 内容: r0, #0
【Hook cpu】 地址: 0x95a0 | 操作码: beq | 内容: #0x1a
hook 基本块
寄存器[R0], hex 值:0
寄存器[R1], hex 值:16a2c
寄存器[R2], hex 值:3
寄存器[R3], hex 值:4
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:95ba
【Hook cpu】 地址: 0x95ba | 操作码: ldr | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x95bc | 操作码: ldr | 内容: r1, [r7, #8]
【Hook cpu】 地址: 0x95be | 操作码: bl | 内容: #0xffffff72
hook 基本块
寄存器[R0], hex 值:0
寄存器[R1], hex 值:5
寄存器[R2], hex 值:3
寄存器[R3], hex 值:4
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:9530
【Hook cpu】 地址: 0x9530 | 操作码: sub | 内容: sp, #0x14
【Hook cpu】 地址: 0x9532 | 操作码: mov | 内容: r2, r1
【Hook cpu】 地址: 0x9534 | 操作码: mov | 内容: r3, r0
【Hook cpu】 地址: 0x9536 | 操作码: str | 内容: r0, [sp, #0x10]
【Hook cpu】 地址: 0x9538 | 操作码: str | 内容: r1, [sp, #0xc]
【Hook cpu】 地址: 0x953a | 操作码: ldr | 内容: r0, [sp, #0x10]
【Hook cpu】 地址: 0x953c | 操作码: ldr | 内容: r1, [sp, #0xc]
【Hook cpu】 地址: 0x953e | 操作码: add | 内容: r0, r1
【Hook cpu】 地址: 0x9540 | 操作码: str | 内容: r0, [sp, #8]
【Hook cpu】 地址: 0x9542 | 操作码: ldr | 内容: r0, [sp, #8]
【Hook cpu】 地址: 0x9544 | 操作码: str | 内容: r2, [sp, #4]
【Hook cpu】 地址: 0x9546 | 操作码: str | 内容: r3, [sp]
【Hook cpu】 地址: 0x9548 | 操作码: add | 内容: sp, #0x14
【Hook cpu】 地址: 0x954a | 操作码: bx | 内容: lr
hook 基本块
寄存器[R0], hex 值:5
寄存器[R1], hex 值:5
寄存器[R2], hex 值:5
寄存器[R3], hex 值:0
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:95c2
【Hook cpu】 地址: 0x95c2 | 操作码: str | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x95c4 | 操作码: ldr | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x95c6 | 操作码: ldr | 内容: r1, [r7, #0xc]
【Hook cpu】 地址: 0x95c8 | 操作码: bl | 内容: #0xffffff68
hook 基本块
寄存器[R0], hex 值:5
寄存器[R1], hex 值:6
寄存器[R2], hex 值:5
寄存器[R3], hex 值:0
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:9530
【Hook cpu】 地址: 0x9530 | 操作码: sub | 内容: sp, #0x14
【Hook cpu】 地址: 0x9532 | 操作码: mov | 内容: r2, r1
【Hook cpu】 地址: 0x9534 | 操作码: mov | 内容: r3, r0
【Hook cpu】 地址: 0x9536 | 操作码: str | 内容: r0, [sp, #0x10]
【Hook cpu】 地址: 0x9538 | 操作码: str | 内容: r1, [sp, #0xc]
【Hook cpu】 地址: 0x953a | 操作码: ldr | 内容: r0, [sp, #0x10]
【Hook cpu】 地址: 0x953c | 操作码: ldr | 内容: r1, [sp, #0xc]
【Hook cpu】 地址: 0x953e | 操作码: add | 内容: r0, r1
【Hook cpu】 地址: 0x9540 | 操作码: str | 内容: r0, [sp, #8]
【Hook cpu】 地址: 0x9542 | 操作码: ldr | 内容: r0, [sp, #8]
【Hook cpu】 地址: 0x9544 | 操作码: str | 内容: r2, [sp, #4]
【Hook cpu】 地址: 0x9546 | 操作码: str | 内容: r3, [sp]
【Hook cpu】 地址: 0x9548 | 操作码: add | 内容: sp, #0x14
【Hook cpu】 地址: 0x954a | 操作码: bx | 内容: lr
hook 基本块
寄存器[R0], hex 值:b
寄存器[R1], hex 值:6
寄存器[R2], hex 值:6
寄存器[R3], hex 值:5
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:95cc
【Hook cpu】 地址: 0x95cc | 操作码: str | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x95ce | 操作码: b | 内容: #2
hook 基本块
寄存器[R0], hex 值:b
寄存器[R1], hex 值:6
寄存器[R2], hex 值:6
寄存器[R3], hex 值:5
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:a02000
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00f98
PC 值:95d0
【Hook cpu】 地址: 0x95d0 | 操作码: ldr | 内容: r0, [sp, #0x1c]
【Hook cpu】 地址: 0x95d2 | 操作码: add | 内容: sp, #0x40
【Hook cpu】 地址: 0x95d4 | 操作码: ldr | 内容: r8, [sp], #4
-------- unicorn 执行后--------
寄存器[R0], hex 值:b
寄存器[R1], hex 值:6
寄存器[R2], hex 值:6
寄存器[R3], hex 值:5
寄存器[R4], hex 值:4
寄存器[R5], hex 值:3
寄存器[R6], hex 值:2
寄存器[R7], hex 值:a00fe8
寄存器[R8], hex 值:0
寄存器[R9], hex 值:0
寄存器[R10], hex 值:0
寄存器[R11], hex 值:0
SP 值:a00fdc
PC 值:95d4
```
**
