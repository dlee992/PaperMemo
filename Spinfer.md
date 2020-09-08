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

### Data analysis: challenging part

- [ ] btrfs_inline
- [ ] dasd_smalloc: 因为聚类的原因，把实际上应该拆分为两条规则的节点放到了一起，导致最终只输出了那个覆盖率大的规则，直接忽略了那个覆盖率小的规则。这例子把函数的定义文件都掏出来作为例子，部分修改就属于需要过滤的noise。

