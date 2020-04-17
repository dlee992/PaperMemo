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

张令明：**ISSTA20已经发表了用修复技术反过来精化缺陷定位技术的工作（反哺）**

## 基于约束求解的技术流派

`SemFix, Nopol, DirectFix, Angelix (2013~2016)`

`SemFix:` use symbolic execution (`delta debugging`) to infer specification,  use `component-based synthesis` to find a patch
$$
x = f_{buggy}(...) → x = f(...) \\
if(f_{buggy}(...)) → if(f(...))
$$
深入研究代码，克服恐慌情绪！



## 基于搜索的技术流派

## 基于机器(深度)学习的技术流派