
一个使用 C++ 实现的 Huffman 编码实验项目，提供文本文件的压缩、解压与基于 BM 算法的模式搜索功能。本 README 同时作为设计报告，记录系统实现思路、关键数据结构、使用方式与实验结果。

## 功能概览

- `-c`: 读取源文件，生成 `.bin` 压缩文件。

- `-d`: 从 `.bin` 文件恢复原文（可选参数限制位长，便于调试与确认搜索是否正确）。

- `-s`: 对压缩文件执行 BM 二进制串搜索，输出匹配出现次数与具体 bit 位置。

  

## 设计思路

### 压缩流程

1. **字符统计**：`getFrequency` 顺序遍历输入文件，统计 `map<char,int>` 频次。

2. **建树编码**：最小堆构造 Huffman 树（`makeHuffmanTree`），递归生成编码表（`generateCodes`）。

3. **树序列化**：先序遍历输出 0/1 标记；叶子节点后附 8 位字符值，得到唯一树描述（`serializeTree`）。

4. **位串拼接**：树描述 + 原文编码串联合成整体 bitstring，在最前方预留 3 位记录补零长度，保证输出按字节对齐。

  

### 压缩文件格式

```

┌──3bits──┬───────────────可变长度────────────────┐

│padding │ serialized tree + encoded payload      │

└──────────────────────────────────────────────────┘

```

`padding` 范围 0~7，表示尾部需要忽略的 bit 数。树的序列化结构允许解码时按顺序重建完全二叉树形态。

  

### 解压流程

1. 逐字节转回字符串 bitstream，解析头 3 位得到 `padding`。

2. 截取有效 payload，调用 `rebuildTree` 依据序列化结果恢复 Huffman 树。

3. 重新按位遍历 payload，以叶子为界输出字符。可传入 `bitLength` 参数仅解码指定长度，方便问题定位。

  

### 搜索流程

1. 与解压前相同，先剥离 padding、重建 Huffman 树、生成编码表。

2. 将目标字符串转换为对应 Huffman 码串 `targetCode`。

3. 在 payload bitstream 上使用 BM 算法搜索，输出命中位置（bit 索引）与次数。

  

### Huffman 码与查询误差

- Huffman 编码是**前缀无二义编码**（prefix-free），但并非后缀唯一：不同字符的编码可能以相同的 bit 序列结尾。直接在位串里搜索短码（例如某个单字符的码）时，BM 有可能在两个字符的交界处找到“伪匹配”，导致误报。为避免这一点，本项目通过“先把整段文本按字符解码，再把目标单词编码”方式保证匹配对齐；若想进一步降低误判，可在搜索结果里复核解码片段。

- 若压缩阶段与搜索阶段的字典不一致（比如压缩文件损坏、树序列化被破坏、或使用了其他文件生成的码表），`targetCode` 与正文位串就不兼容，会出现“找不到/全部错配”。

- `target` 中包含从未在源文件中出现的字符时，码表无条目，只能提示“字符未编码”并终止搜索。

- BM 只实现坏字符表，若 `targetCode` 很短或文本接近随机（熵高），跳步空间有限，几乎要逐位扫描；而当编码长度较长、且码串模式稀疏时，坏字符表能一次跳过多个字节，效率明显提升。

- 如果文件头的 padding 值错误，末尾补齐位未正确剥离，搜索会把这些多余位串当成正文，导致“尾部 ghost 匹配”。

  

### BM 算法要点

`BMsearch.cpp` 仅实现坏字符规则：

- 预处理 256 长度 `badChar` 数组，记录模式字符最后出现位置。

- 从右向左比较，失配时根据 `j - badChar[text[s+j]]` 决定跳步。

- 匹配成功返回首次出现位置；若需多次匹配，用上一命中位置后的子串继续搜索。

  

## 主要数据结构与函数

| 模块 | 描述 |

| --- | --- |

| `HuffmanNode` | 保存字符、频次及左右子指针的节点结构。 |

| `Huffman::compress` | 压缩入口，负责建树、编码、序列化及输出字节流。 |

| `Huffman::decompress` | 解压入口，处理 padding、重建树并输出原文。 |

| `Huffman::search` | 结合 Huffman 码表与 BM 算法完成目标字符串查找。 |

| `serializeTree` / `rebuildTree` | 树的序列化与反序列化，保证压缩文件自描述。 |

| `BMsearch` | 坏字符版 Boyer–Moore，实现位串层面的模式匹配。 |

  

## 构建与运行

1. **编译**

    ```bash

    g++ -std=c++17 main.cpp huffman.cpp BMsearch.cpp help.cpp -o main

    ```

2. **压缩**

    ```bash

    ./main -c test.txt

    # 生成 test.txt.bin

    ```

3. **解压**

    ```bash

    ./main -d test.txt.bin           # 输出 test.txt.bin.decomp

    ./main -d test.txt.bin 200       # 仅解出前 200

    ```

4. **搜索**

    ```bash

    ./main -s test.txt.bin

    Enter the target string to search: lorem

    ```

5. **一致性校验**

    ```bash

    # diff -s 会在内容一致时打印 “Files ... are identical”

    diff -s test.txt test.txt.bin.decomp

    ```

    ![alt text](image.png)
    
6. **计算压缩率**

    ```bash

    # 统计原文件与压缩文件的字节数

    stat -c %s test.txt            # 原文件大小 (bytes)

    stat -c %s test.txt.bin        # 压缩文件大小 (bytes)

  

    # 也可使用 wc -c，它会在末尾附上文件名

    wc -c test.txt test.txt.bin

    ```

    ![alt text](compress_radio.png)

  

## 实验与验证

- `test.txt` 作为示例输入，压缩后得到 `test.txt.bin`，再解压为 `test.txt.bin.decomp`，与原文一致。

- 搜索模式 `BM` 或任意单词，可从控制台观察到 `Target code` 及多次命中位置。

![[image-1.png]]