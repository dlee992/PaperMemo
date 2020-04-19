# Automatic Program Repair

## Framework (fix generation step)

*(for generate-and-validate techniques) How to design search space sampler?*

*(for semantics-driven techniques) How to collect program constraints?*

*(Machine learning-based techniques) ?*

## 2020-03-20

框架如何确定？基本点还是从 exploit example-based templates for automatic program repair 出发来考虑，这个方法的基本框架呢？想一想。

## 2020-04-16

国内外做修复研究的老师的探讨 2019-10-19，https://mp.weixin.qq.com/s/vKeCbfKH6HK3LifhLdhlnA。

熊英飞：**现在约束求解都是以SAT为核心把不同solver整合起来，而SAT主要算法CDCL都是启发式搜索。**

张令明：**ISSTA'20已经发表了用修复技术反过来精化缺陷定位技术的工作（反哺）**

## 基于约束求解的技术流派

`SemFix, Nopol, DirectFix, Angelix (2013~2016)`

`SemFix:` use symbolic execution (`delta debugging`) to infer specification,  use `component-based synthesis` to find a patch
$$
x = f_{buggy}(...) → x = f(...) \\
if(f_{buggy}(...)) → if(f(...))
$$
深入研究代码，克服恐慌情绪！

看上去这是一篇毫无新意的工作，整个思路包含的部分：`统计错误定位`＋`符号执行`＋`程序合成`，似乎一气呵成．而且实验部分还提到，`component-based synthesis`只比纯枚举的合成策略快常数倍(`x4`)，这简直让人怀疑component-based算法带来的复杂的编码格式究竟还有何意义．

首先有个待修复的程序 buggy program, 然后有个单元测试集 test suite;先用统计的方式概率性的定位出可能出错的代码行号(Tarantula), 得出可能出错的 statement ranking; 然后用KLEE(符号执行工具)结合具体的test suite和程序里的assert语句,来给出符号化的repair constraint; 最后利用程序合成(program synthesis)的方式找到符合条件的表达式或者if-condition, 最终验证得到的repair是否能通过所有的test suite.

` DirectFix`

`Angelix`



## 基于搜索的技术流派

## 基于机器(深度)学习的技术流派