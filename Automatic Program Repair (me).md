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

`SemFix, Nopol, DirectFix, Angelix, SearchRepair, JFix, S3 (2013~2016)`

`SemFix:` use symbolic execution (`delta debugging`) to infer specification,  use `component-based synthesis` to find a patch
$$
x = f_{buggy}(...) → x = f(...) \\
if(f_{buggy}(...)) → if(f(...))
$$
深入研究代码，克服恐慌情绪！

看上去这是一篇毫无新意的工作，整个思路包含的部分：`统计错误定位`＋`符号执行`＋`程序合成`，似乎一气呵成．而且实验部分还提到，`component-based synthesis`只比纯枚举的合成策略快常数倍(`x4`)，这简直让人怀疑component-based算法带来的复杂的编码格式究竟还有何意义．

首先有个待修复的程序 buggy program, 然后有个单元测试集 test suite;先用统计的方式概率性的定位出可能出错的代码行号(Tarantula), 得出可能出错的 statement ranking; 然后用KLEE(符号执行工具)结合具体的test suite和程序里的assert语句,来给出符号化的repair constraint; 最后利用程序合成(program synthesis)的方式找到符合条件的表达式或者if-condition, 最终验证得到的repair是否能通过所有的test suite.

` DirectFix:`感觉这是一篇不奏效的工作, 如果可以把多个位置的表达式抽象成符号,然后编码成约束,但是又将不同组的test  case的变量用不同符号替换,这和单独求解又有什么区别呢?而且当位置很多时,这时候不还是会导致约束空间爆炸么?

`Angelix:` 被符号化的表达式的路径条件`path condition(program state before the evaluation of this expression)`是否可能包含之前被符号化的表达式呢?如果是的话, 多次迭代之后, 这个路径条件岂不是很复杂?

这文章说了半天`our novel lightweight program-size-independent semantic signature is the improvement of the heavyweight semantic signature used in our prior work DirectFix`, 但是我没看明白究竟哪里体现了轻量级的特性. OK, for solving my question, I find a video from `Claire Le Goues`, 她不是原作者,但是她的演讲里提到了这份工作.

In this video, she said: `angelix cares about path condition changes, not path condition itself, so the size of path condition is independent with program size.` 我感觉好像理解了,但又不是非常确信,总觉得在最坏情况下,这两者的大小并不是独立的.

`JFix:` 这是一个基于 `Java Symbolic PathFinder (SPF)` 写的一个基于语义的程序合成工具(框架), 一篇 Tool Demo. 集成了 `Angelix` 和 另外两个 `SyGuS` 的 `solvers`.

`SearchRepair:`  这篇文章搜索 `SMT-encoded code database`,返回相符的代码片段, 然后尝试改编这些代码作为补丁修复的方案. `Semantic` + `Search`. 从实验对象上看，依然只能修复简单程序（返回几个数中的最大数）．这篇工作的 `Constraint Encoding` 和 `code fragment searching` 看上去都太暴力了, 该实现机制虽然美好, 但是实现策略几乎都是暴力, 非常难以应用到真实程序中.  对很多实际情况的考虑估计都很难周到. 每个`benchmark`程序的`KLEE test`数量不超过10个,足见代码片段之小.

## 基于搜索的技术流派

`S3,`

`S3:`这文章指出`Angelix`的问题, 



## 基于机器(深度)学习的技术流派