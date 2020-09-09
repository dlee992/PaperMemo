# Spinfer

## Algorithm

1. 不能有效处理MACRO在字符串级别的变化：但太细微了，有意义吗？
2. 看上去rule ordering的方式有点问题？应该没啥问题
3. 虽然在CFG中提到了对condition的建模（处理分支和循环），但是实验中有吗？
4. 注意理解控制依赖和分支/循环结构的区别，这不是同一件事，顺序结构的语句之间也有控制依赖。
5. 对于context的数据依赖分析很远，能从函数签名（数据定义）---> 函数体中的某一处使用（详见dasd_smalloc/reduce)
6. 并没有处理好多个variants的挑战（详见dasd_smalloc/full）
7. overlapping change instances in per function的处理方式我还没理解

## Implementation

## Evaluation

expect.cocci 也不是完美补丁

实验中反复提到out of scope的测试用例，但是又没有标明具体是哪些测试用例

recall / precision / matching recall 是如何计算出来的？迷惑行为，FP/TP的计量单位是 ***function***  or example?

### Data analysis: challenging part

- [ ] btrfs_inline: full中的规则包含多种变体，删除参数比较简单，甚至能表达出pr_info(str_format, ...)中 str_format 的差异。reduced中合成的恰好是full中的第二条规则，但是在最终生成规则时，又进行了简化（允许简化的条件是什么？竟然还有简化这一步骤？），只保留了必要的表达式。reduced因为没有见过涉及到str_format的例子，因此在testing过程中无法成功转换pr_info(str_format, ...)这条语句。
- [ ] cleanoom: 删除了一个OOM的错误输出语句，full中合成的规则2,**先删除了if (E0) 然后又添加了回来**，这是个实现bug？这组合成的难点在于虽然都是删除一个输出语句，其中某个参数都是str_format，并且多数含有"memory"的子串，通常被包裹在一个分支语句内，其他并无用于生成规则的明显特征了。reduced中也发生了先删除了if (E0) 然后又添加了回来的问题。
- [ ] copy_from_user: 使用 **memdup_user(...)** 来替换 **kmalloc(...) 和 copy_from_user(...)** 的组合。凡涉及内存读写的，都要对返回值进行错误处理，大体是这么个情况。得到 memdup_user() 的返回值之后，有的函数直接返回对应的结构体指针，有的则是继续进行其他计算，因而产生了**变体**。对分支语句的大括号的处理？感觉rules对大括号的处理有些瑕疵，对大括号的删减并不是有正确代码层次的删减。在if体里还会掺杂一些外部的memory free语句，因而产生了**变体**。这时候不同规则的执行顺序也变得更加重要，否则会产生 False Positives。最后提到的 applied partially 情况是当某个变量的使用被删除时，对应的变量定义语句没有删除。当某个更改存在两种变体时，如果规则没有足够的上下文，会导致两条规则互相掣肘，必然导致某一条规则无法产生，因为占比较小，而占比大的规则会胜出，保留下来，但同时也会产生false positives，（详见1281399506_2010-08-09_90d7404558fb_util.c: strndup_user），打破掣肘，需要考虑函数的返回值类型，因为这个问题出现在return语句上。
- [ ] d_instantiate:  看上去几近完美，为啥recall率没达到1,为啥函数还有1个漏网之鱼？
- [ ] dasd_smalloc: （现象的确如此，但分析不合理）因为规则先后顺序的原因，把实际上应该拆分为两条规则的节点放到了一起，导致最终只输出了那个覆盖率大的规则，直接忽略了那个覆盖率小的规则。这例子把函数的定义文件都掏出来作为例子，部分修改就属于需要过滤的noise。打破掣肘，需要考虑函数定义中的arg list是否有个特定类型的参数。
- [ ] devm_request_and_ioremap: 



### Data analysis: 2018 part

- [ ] 

