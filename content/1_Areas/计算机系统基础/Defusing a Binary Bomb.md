### 一、实验目的

1. 理解 C 语言程序的机器级表示。
2. 初步掌握 GDB 调试器的用法。
3. 阅读 C 编译器生成的 x86-64 机器代码，理解不同控制结构生成的基本指令模式，以及过程的实现。

### 二、实验环境

1. **服务器**：
   - IP：`10.112.61.28`（X86版本）
2. **操作系统**：Linux
3. **工具**：
   - `objdump` 命令反汇编
   - `GDB` 调试工具
4. **积分榜**：[http://10.112.152.199:19210/scoreboard](http://10.112.152.199:19210/scoreboard)

### 三、实验内容

登录 `bupt1` 服务器，在 home 目录下可以找到 Evil 博士专门为你量身定制的一个 `bomb`。

当运行时，它会要求你输入一个字符串：
- 如果正确，则进入下一关，继续要求你输入下一个字符串；
- 否则，炸弹就会爆炸，输出一行提示信息并向计分服务器提交扣分信息。

**实验要求：**
必须通过反汇编和逆向工程对 bomb 执行文件进行分析，找到正确的字符串来解除这个的炸弹。

本实验通过要求使用课程所学知识拆除一个“binary bombs”来增强对程序的机器级表示、汇编语言、调试器和逆向工程等方面原理与技能的掌握。“binary bombs”是一个 Linux 可执行程序，包含了 5 个阶段（或关卡）。炸弹运行的每个阶段要求你输入一个特定字符串，你的输入符合程序预期的输入，该阶段的炸弹就被拆除引信；否则炸弹“爆炸”，打印输出 “BOOM!!!”。炸弹的每个阶段考察了机器级程序语言的一个不同方面，难度逐级递增。

为完成二进制炸弹拆除任务，需要使用 `gdb` 调试器和 `objdump` 来反汇编 `bomb` 文件，可以单步跟踪调试每一阶段的机器代码，也可以阅读反汇编代码，从中理解每一汇编语言代码的行为或作用，进而设法推断拆除炸弹所需的目标字符串。

### 四、实验步骤及实验分析

#### 准备工作

1. 为了了解炸弹程序的整体结构，我使用了 `objdump` 工具查看程序的符号表。通过 `grep` 过滤关键字 `phase`，我发现程序定义了 `phase_1` 到 `phase_6` 共 6 个主要的关卡函数，以及一个隐藏的 `secret_phase` 函数。明确了本次实验需要攻克的 7 个主要目标。

    ```bash
    objdump -t bomb | grep phase
    ```
    ![alt text](<Pasted image 20251121192846.png>)
2. 在运行程序之前，我还使用 `strings` 命令提取了二进制文件中的所有可打印字符串，试图寻找明文线索。结果发现了程序的欢迎信息、通关提示语，甚至直接发现了 `Phase 1` 的明文密码字符串，这为后续的调试提供了直接的切入点。

#### Phase_1
首先，我在 phase_1 函数入口处设置断点并运行程序。停在断点后，使用 disas 命令查看反汇编代码。
    ![alt text](<Pasted image 20251121193616.png>)
汇编代码发现，程序在 <+11> 处调用了 `strings_not_equal` 函数。根据函数名推测，这是一个字符串比较操作。根据 x86-64 调用约定，函数调用的第一个参数（用户输入）存放在 `%rdi` 寄存器中，第二个参数（目标字符串）存放在 `%rsi` 寄存器中。
在 <+4> 处，指令 `lea 0x17ba(%rip), %rsi` 将一个内存地址加载到了 `%rsi` 中。这意味着 `%rsi` 指向的就是我们需要匹配的目标字符串。
为了获取目标字符串的具体内容，我使用 GDB 的 `x/s (Examine String)` 命令查看指向的内存地址`0x2aa0`
GDB 直接打印出了明文字符串："**The bomblab forces students to learn how to use a debugger.**"
这正是 Phase 1 所要求的正确输入。

#### Phase_2
进入 Phase 2 后，首先通过反汇编分析其输入要求。
~~~ assembly
Dump of assembler code for function phase_2:
   0x00005555555552f9 <+0>:     push   %rbp
   0x00005555555552fa <+1>:     push   %rbx
   0x00005555555552fb <+2>:     sub    $0x28,%rsp
   0x00005555555552ff <+6>:     mov    %fs:0x28,%rax    //这个应该存储的是金丝雀
   0x0000555555555308 <+15>:    mov    %rax,0x18(%rsp)  //可以使用的栈帧大小： (16+8) / 4 = 6 个 int 的整数
   0x000055555555530d <+20>:    xor    %eax,%eax
   0x000055555555530f <+22>:    mov    %rsp,%rsi
   0x0000555555555312 <+25>:    callq  0x555555555ac0 <read_six_numbers>// 6 个数字将被存储在栈上 %rsp 指向的位置 
   0x0000555555555317 <+30>:    cmpl   $0x0,(%rsp)      // 第一个数必须是 0
   0x000055555555531b <+34>:    jne    0x555555555324 <phase_2+43>
   0x000055555555531d <+36>:    cmpl   $0x1,0x4(%rsp)   // 第二个数必须是 1   
   0x0000555555555322 <+41>:    je     0x555555555329 <phase_2+48>
   0x0000555555555324 <+43>:    callq  0x555555555a84 <explode_bomb>
   0x0000555555555329 <+48>:    mov    %rsp,%rbx                    // int i = 0; rbx = rsp[0]
   0x000055555555532c <+51>:    lea    0x10(%rsp),%rbp              // while (i < 4)
   0x0000555555555331 <+56>:    mov    0x4(%rbx),%eax
   0x0000555555555334 <+59>:    add    (%rbx),%eax                  // %eax = rsp[i] + rsp[i+1]
   0x0000555555555336 <+61>:    cmp    %eax,0x8(%rbx)               // rsp[i+2]必须等于rsp[i] + rsp[i+1]
   0x0000555555555339 <+64>:    je     0x555555555340 <phase_2+71>
   0x000055555555533b <+66>:    callq  0x555555555a84 <explode_bomb>
   0x0000555555555340 <+71>:    add    $0x4,%rbx
   0x0000555555555344 <+75>:    cmp    %rbp,%rbx
   0x0000555555555347 <+78>:    jne    0x555555555331 <phase_2+56>  //
   0x0000555555555349 <+80>:    mov    0x18(%rsp),%rax  // 检查有没有栈溢出
   0x000055555555534e <+85>:    xor    %fs:0x28,%rax
   0x0000555555555357 <+94>:    je     0x55555555535e <phase_2+101>
   0x0000555555555359 <+96>:    callq  0x555555554f00 <__stack_chk_fail@plt>
   0x000055555555535e <+101>:   add    $0x28,%rsp
   0x0000555555555362 <+105>:   pop    %rbx
   0x0000555555555363 <+106>:   pop    %rbp
   0x0000555555555364 <+107>:   retq
~~~
我们可以顺利推断出我们需要的是首项为 0，第二项为 1 的斐波那契数列规律，推导出完整的 6 个数字为：**0 1 1 2 3 5**

#### Phase_3
反汇编 Phase 3，首先注意到程序调用了 `sscanf`。通过查看 `%rsi` 指向的格式化字符串 "%d %d"，确定本关需要输入两个整数。
```
...
   0x0000555555555379 <+20>:    lea    0x4(%rsp),%rcx
   0x000055555555537e <+25>:    mov    %rsp,%rdx
   0x0000555555555381 <+28>:    lea    0x1a05(%rip),%rsi        # 0x555555556d8d
   0x0000555555555388 <+35>:    callq  0x555555554fa0 <__isoc99_sscanf@plt>
   0x000055555555538d <+40>:    cmp    $0x1,%eax
   0x0000555555555390 <+43>:    jg     0x555555555397 <phase_3+50>
   0x0000555555555392 <+45>:    callq  0x555555555a84 <explode_bomb>
   0x0000555555555397 <+50>:    cmpl   $0x7,(%rsp)  // 0 <= 第一个数必须 <= 7
   0x000055555555539b <+54>:    ja     0x5555555553e1 <phase_3+124>
   0x000055555555539d <+56>:    mov    (%rsp),%edx
   0x00005555555553a0 <+59>:    lea    0x1769(%rip),%rax        # 0x555555556b10
   0x00005555555553a7 <+66>:    movslq (%rax,%rdx,4),%rdx
   0x00005555555553ab <+70>:    add    %rdx,%rax
   0x00005555555553ae <+73>:    jmpq   *%rax        // swich case 的跳转表 
   0x00005555555553b0 <+75>:    mov    $0x4b,%eax
   0x00005555555553b5 <+80>:    jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553b7 <+82>:    mov    $0x22c,%eax
   0x00005555555553bc <+87>:    jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553be <+89>:    mov    $0x19c,%eax
   0x00005555555553c3 <+94>:    jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553c5 <+96>:    mov    $0x1ce,%eax
   0x00005555555553ca <+101>:   jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553cc <+103>:   mov    $0x16c,%eax
   0x00005555555553d1 <+108>:   jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553d3 <+110>:   mov    $0x38b,%eax
   0x00005555555553d8 <+115>:   jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553da <+117>:   mov    $0x28c,%eax
   0x00005555555553df <+122>:   jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553e1 <+124>:   callq  0x555555555a84 <explode_bomb>
   0x00005555555553e6 <+129>:   mov    $0x0,%eax
   0x00005555555553eb <+134>:   jmp    0x5555555553f2 <phase_3+141>
   0x00005555555553ed <+136>:   mov    $0x6f,%eax
   0x00005555555553f2 <+141>:   cmp    0x4(%rsp),%eax   
   // 其实我们只需要在这里打断点利用 info reg %eax 看一下此时需要的 第二个数是多少就行了 qwq
   0x00005555555553f6 <+145>:   je     0x5555555553fd <phase_3+152>
...
```
然后我们得到了正确的答案 **0 111**

#### Phase_4
与 Phase 3 相同，我们容易得到需要输入是两个整数。其中
```
Dump of assembler code for function phase_4:
...
   0x000055555555545e <+20>:    lea    0x4(%rsp),%rcx
   0x0000555555555463 <+25>:    mov    %rsp,%rdx
   0x0000555555555466 <+28>:    lea    0x1920(%rip),%rsi        # 0x555555556d8d
   0x000055555555546d <+35>:    callq  0x555555554fa0 <__isoc99_sscanf@plt>
   0x0000555555555472 <+40>:    cmp    $0x2,%eax    // 进一步佐证我们需要输入两个整数
   0x0000555555555475 <+43>:    jne    0x55555555547d <phase_4+51>
   0x0000555555555477 <+45>:    cmpl   $0xe,(%rsp)  // 第一个数 x <= 14
   0x000055555555547b <+49>:    jbe    0x555555555482 <phase_4+56>
   0x000055555555547d <+51>:    callq  0x555555555a84 <explode_bomb>
   0x0000555555555482 <+56>:    mov    $0xe,%edx    
   0x0000555555555487 <+61>:    mov    $0x0,%esi
   0x000055555555548c <+66>:    mov    (%rsp),%edi
   0x000055555555548e <+69>:    callq  0x555555555417 <func4>   
   0x0000555555555494 <+74>:    cmp    $0x25,%eax   // func4( x, 0，14) == 0x25
   0x0000555555555497 <+77>:    jne    0x5555555554a0 <phase_4+86>
   0x0000555555555499 <+79>:    cmpl   $0x25,0x4(%rsp)
   0x000055555555549e <+84>:    je     0x5555555554a5 <phase_4+91> // 第二个数必须是 2*16 + 5 = 37 
...
```
我们暂时得到`func4( x, 0，14) == 0x25` ,且第二个数需要时 37 。我们继续查看 func4 的反汇编。
```
   Dump of assembler code for function func4:
   0x0000555555555417 <+0>:     push   %rbx
   0x0000555555555418 <+1>:     mov    %edx,%eax    
   0x000055555555541a <+3>:     sub    %esi,%eax    // eax = edx - esi
   0x000055555555541c <+5>:     mov    %eax,%ebx
   0x000055555555541e <+7>:     shr    $0x1f,%ebx   // ebx >> 31
   0x0000555555555421 <+10>:    add    %ebx,%eax    // eax + (eax 的符号位)
   0x0000555555555423 <+12>:    sar    %eax         // eax >> 1 
   // ——> 综合可以得出 eax = eax / 2 达到向零取整的目的
   0x0000555555555425 <+14>:    lea    (%rax,%rsi,1),%ebx   // ebx = (edx + esi)/ 2
   0x0000555555555428 <+17>:    cmp    %edi,%ebx
   0x000055555555542a <+19>:    jle    0x555555555438 <func4+33>
   0x000055555555542c <+21>:    lea    -0x1(%rbx),%edx      // 
   0x000055555555542f <+24>:    callq  0x555555555417 <func4>   // func4(rdi,rsi,ebx-1)
   0x0000555555555434 <+29>:    add    %ebx,%eax                
   0x0000555555555436 <+31>:    jmp    0x555555555448 <func4+49>    // return ebx + func;
   0x0000555555555438 <+33>:    mov    %ebx,%eax            //  if (ebx <= edi ) eax = ebx
   0x000055555555543a <+35>:    cmp    %edi,%ebx            
   0x000055555555543c <+37>:    jge    0x555555555448 <func4+49> if (ebx == edi) return ebx;
   0x000055555555543e <+39>:    lea    0x1(%rbx),%esi             else func4(rdi,ebx+1,rdx)
   0x0000555555555441 <+42>:    callq  0x555555555417 <func4>
   0x0000555555555446 <+47>:    add    %ebx,%eax            // return half + func;
   0x0000555555555448 <+49>:    pop    %rbx
   0x0000555555555449 <+50>:    retq
End of assembler dump.
```

我们可以分析出来func4 的大致代码,是一个类似于二分查找。
``` cpp
int func(int target,int left , int right){
    // left 对应 rsi，right 对应 rdx
    int half = (left + right);
    if (half > target) {
        return half + func4(target,left,half-1);
    }
    else if (half < target) {
        func4(target,half+1,right);
        return half + func4(target,left,half-1);
    }
    else return half;
}
```
为了找到使得返回值为 37 的输入 x，我分析了二分查找的路径求和逻辑。
首先，根节点的中间值是 7。
若 x < 7，1+2+3+4+5+6 = 21, 21+7 = 28 < 37 , 所以 x > 7 ,下一层中间值为 11，累计和为 18。
若 x > 11 ，再下一层中间值为 13，累计和为 31。还差 37 - 31 = 6 但是不可能满足。 所以 x < 11 ,此时距离目标 37 还差 10。计算下一层区间 [10,10] 的中间值恰好为 10。
若 x = 10，则递归终止，加上最后的 10，总和正好为 37。因此推断出输入为 **10 37**

#### Phase_5
先分析 Phase 5 的反汇编。
```
Dump of assembler code for function phase_5:
...
   0x00005555555554c4 <+5>:     mov    %rdi,%rbx
...
   0x00005555555554d7 <+24>:    callq  0x555555555767 <string_length>
   0x00005555555554dc <+29>:    cmp    $0x6,%eax    // 本关输入的必须是长度为 6 的字符串
   0x00005555555554df <+32>:    je     0x5555555554e6 <phase_5+39>
   0x00005555555554e1 <+34>:    callq  0x555555555a84 <explode_bomb>
   0x00005555555554e6 <+39>:    mov    $0x0,%eax    // int eax = 0;
   0x00005555555554eb <+44>:    movzbl (%rbx,%rax,1),%edx   // edx = rbx + rax
   0x00005555555554ef <+48>:    and    $0xf,%edx            // edx 保留最低位的四位作为索引
   0x00005555555554f2 <+51>:    lea    0x1637(%rip),%rcx        # 0x555555556b30 <array.3461>
   0x00005555555554f9 <+58>:    movzbl (%rcx,%rdx,1),%edx   // edx = rcx + rdx
   0x00005555555554fd <+62>:    mov    %dl,0x1(%rsp,%rax,1) // rsp[rax + 1] = array[edx];
   0x0000555555555501 <+66>:    add    $0x1,%rax            // rax++;
   0x0000555555555505 <+70>:    cmp    $0x6,%rax            // rax < 6;
   0x0000555555555509 <+74>:    jne    0x5555555554eb <phase_5+44>
   0x000055555555550b <+76>:    movb   $0x0,0x7(%rsp)       // rsp[6] = "\0";
   0x0000555555555510 <+81>:    lea    0x1(%rsp),%rdi       // rdi = rsp + 1;
   0x0000555555555515 <+86>:    lea    0x15ea(%rip),%rsi        # 0x555555556b06
   0x000055555555551c <+93>:    callq  0x555555555785 <strings_not_equal> 
   0x0000555555555521 <+98>:    test   %eax,%eax
   0x0000555555555523 <+100>:   je     0x55555555552a <phase_5+107>
   0x0000555555555525 <+102>:   callq  0x555555555a84 <explode_bomb>
...
```
即通过我们输入的字符的ASCII编码的最后四位作为四位二进制的序号，查找密码表
通过 GDB 查看内存，我找到了加密所需的关键数据：密码表: 地址 0x555555556b30，内容为 "maduiersnfotvbyl"。
目标字符串: 地址 0x555555556b06，内容为 "flames"。程序的最后一步调用 `strings_not_equal`，要求我们的输入经过加密后，必须等于 "flames"。
![alt text](<Pasted image 20251121204515.png>)
![alt text](<Pasted image 20251121204425.png>)
列出下面的表格，然后再查[ASCII码表](https://c.biancheng.net/c/ascii/)，得到答案：**ioapeg**
| 结果  | f | l | a | m | e | s |
|:-----:|:---:|:---:|:---:|:---:|:---:|:---:|
|序号(d)| 9 | 15 | 1 |0 | 5 |7|
|序号(b)| 1001 |1111|0001|0000|0101|0111|
|输入|i|o|a|p|e|g|

#### Phase_6
反汇编 Phase 6，首先发现它依然调用了 read_six_numbers，说明输入是 6 个整数。
紧接着，代码进入了一个双重嵌套循环（从 <+43> 到 <+104>）
在检查通过后，程序进入了一个关键的数据处理阶段。
指令 `lea 0x202c51(%rip)`, `%rdx` 加载了一个名为 node1 的全局变量地址。这强烈暗示了链表 (Linked List) 结构的存在。
接下来的循环根据用户输入的数字（作为索引），将对应的节点指针（如 &node3, &node1...）按顺序存储到栈上的一个指针数组中。
随后（<+161> 到 <+197>），程序遍历这个指针数组，通过修改每个节点的 next 指针（偏移量 0x8），将这 6 个节点重新链接成一个新的链表。
简而言之，程序根据我的输入顺序，重排了链表。
重组完成后，程序进入最后的验证循环（<+207> 开始）。
核心指令 `cmp %eax`, `(%rbx)` 比较了当前节点的值 (%rbx) 和下一个节点的值 %eax（来自 0x8(%rbx)）。
跳转指令 `jle` 表明：如果 `next_val <= curr_val`，则跳转继续检查；否则爆炸。
我使用 GDB 查看了 node1 到 node6 的内存内容：
![alt text](<Pasted image 20251121204827.png>)

| Node | Hex Value | Decimal Value |Rank |
| :--- | :--- | :--- | :--- |
| **node1**  | `0x256` | **598** | 4 |
| **node2**  | `0x2dd` | **733** | 5 |
| **node3** | `0x30b` | **779** | 6 |
| **node4** | `0x53` | **83** | 1 |
| **node5**  | `0x177` | **375** | 3 |
| **node6**  | `0xbc` | **188** | 2 |

83 (Node 4) < 188 (Node 6) < 375 (Node 5) < 598 (Node 1) < 733 (Node 2) < 779 (Node 3) ，则最终输入序列**4 6 5 1 2 3**

![alt text](<Pasted image 20251121194457.png>)

##### Secret_Phase
在完成所有常规关卡后，我反汇编了 phase_defused 函数。分析发现，当 6 个关卡全部通过后，程序会再次读取 Phase 4 的输入字符串（地址偏移量 240）。
程序使用 sscanf 和格式化字符串 "%d %d %s" 尝试解析输入。如果解析出 3 个参数，且第三个参数（字符串）与硬编码的 "DrEvil" 相匹配，程序将解锁隐藏关卡。

```
Dump of assembler code for function phase_defused:
...
   0x0000555555555c78 <+61>:    lea    0x202b31(%rip),%rdi        # 0x5555557587b0 <input_strings+240>
...
End of assembler dump.
```
![alt text](<Pasted image 20251121205524.png>)
因此，我在 solutions.txt 的 Phase 4 答案后追加了字符串 DrEvil，成功触发了 Secret Phase。
进入 secret_phase 后，发现它调用了一个名为 fun7 的递归函数。
```
   Dump of assembler code for function fun7:
   0x0000555555555651 <+0>:     sub    $0x8,%rsp
   0x0000555555555655 <+4>:     test   %rdi,%rdi                
   0x0000555555555658 <+7>:     je     0x555555555685 <fun7+52> 
   0x000055555555565a <+9>:     mov    (%rdi),%edx              // edx = node->value
   0x000055555555565c <+11>:    cmp    %esi,%edx                
   0x000055555555565e <+13>:    jle    0x55555555566d <fun7+28>
   0x0000555555555660 <+15>:    mov    0x8(%rdi),%rdi           // node->value > target ,node = node->left 
   0x0000555555555664 <+19>:    callq  0x555555555651 <fun7>    // fun7(node->left, esi)
   0x0000555555555669 <+24>:    add    %eax,%eax                // return 2 * fun7
   0x000055555555566b <+26>:    jmp    0x55555555568a <fun7+57> 
   0x000055555555566d <+28>:    mov    $0x0,%eax               
   0x0000555555555672 <+33>:    cmp    %esi,%edx                // esi == esx return 0
   0x0000555555555674 <+35>:    je     0x55555555568a <fun7+57> 
   0x0000555555555676 <+37>:    mov    0x10(%rdi),%rdi          // node->value < target ,node = node->right
   0x000055555555567a <+41>:    callq  0x555555555651 <fun7>    // fun7(node->right, esi)
   0x000055555555567f <+46>:    lea    0x1(%rax,%rax,1),%eax    // return 2 * fun7 + 1
   0x0000555555555683 <+50>:    jmp    0x55555555568a <fun7+57> // 跳转到函数末尾
   0x0000555555555685 <+52>:    mov    $0xffffffff,%eax         // node == NULL, return -1
   0x000055555555568a <+57>:    add    $0x8,%rsp
   0x000055555555568e <+61>:    retq
```
fun7 接收两个参数：一个数据结构指针（n1）和一个整数（用户输入）。
``` cpp
struct TreeNode {
    int value;              // 节点存储的值 (偏移量 0x0)
    struct TreeNode* left;  // 左子节点指针 (偏移量 0x8)
    struct TreeNode* right; // 右子节点指针 (偏移量 0x10)
};

int fun7(struct TreeNode* node, int target) {
    if (node == NULL) {
        return -1;
    }
    if (node->value > target) {
        return 2 * fun7(node->left, target) ;
    } 
    else if (node->value < target) {
        ;
        return 2 * fun7(node->right, target) + 1;
    } 
    return 0;
    
}
```
这不仅是一个二叉搜索树查找，其返回值还编码了查找路径。
secret_phase 要求 fun7 的返回值必须为 4 (二进制 100)。
![alt text](<Pasted image 20251121205317.png>)
根据目标返回值 4 (100) 和递归逻辑推导路径：
使用 GDB 查看内存中的二叉树 n1：根节点 (36) -> 左子节点-> 左子节点 -> 右子节点。最终找到该节点的值为 7。经验证，输入 7 成功通过隐藏关卡。
![alt text](<Pasted image 20251121205536.png>)