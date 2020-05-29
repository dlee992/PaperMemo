# C Preprocessor

如何在重构或者程序分析的过程中保留cpp原语(directive execution and marco expansion during compiling)非常困难.

本质上,因为cpp grammar和C grammar是两个完全不搭嘎的语法,从definition到parsing都完全不同.

[!!!]目前没有(?)任何商用/开源工具能够完美处理带有cpp原语的C代码重构.

所以本次报告应当对cpp的处理有一个清晰的认识,处理到什么程度算较为深入,什么程度算牵线处理.

## 已有工具

### Coccinelle

这个由Julia Lawall带领开发的OCaml工具,用于C代码尤其是驱动代码的重构.

但这个工具自己独立编写了针对C和cpp的parser,看博客似乎他们对marco的处理是手工的(?).

首先,kernel里的marco call,应该不会破坏c的语法格式.但是对于条件编译来说,这貌似就不好说了,比如因为条件编译,在不同分支里都对某个变量进行声明和赋值.

### XRefactory



### srcML

这个工具看起来还不错,可以解析单文件内的宏定义和宏调用(?),

对于不符合C语法的语句,会取反假设它是宏调用,比如list_for_each宏,这个宏在使用的时候是,很好的解析思路,这样不管这个代码gcc/clang能否编译成功,srcML都能给出解析结果(with certain of error recovery):

```C
list_for_each(pointer, head) {
    stmt;
    ...
}
```

这个工具将source code转换成 XML格式的AST,之后还可以用处理XML的工具去实现一些功能,比如:

1. **XPath** for constructing source-code queries
2. **XSLT** for conducting simple transformations

### srcType

这个工具还能做静态类型解析,这里解析特指能够得出程序中function和identifier的变量,对于需要类型推导的程序片段似乎暂不处理.

工具编译了,但不知道怎么执行...

## 已有论文

### P. A. Darnell and P. E. Margolis, “The C Preprocessor,” 1991.

已阅. cpp的存在有历史必然性,主要是通过hard-code的方式来解决C语言在设计之初未能妥善处理的issues.

然而,时至今日,却给C代码的重构带来了很大的困难.

### M. D. Ernst, G. J. Badros, and D. Notkin, “An Empirical Analysis of C Preprocessor Use,” *TOSEM*, pp. 1–34, 2000.

实证研究:分析多个应用软件包中对directives和marcos的使用情况,它的实验设置需要细细研究一下.

粗度了一下,感觉比较中规中矩?文章里给出的统计图标还是值得简单看一看,了解一下.

### A. Garrido and R. Johnson, “Refactoring C with Conditional Compilation,” *ASE short*, 2003.

### A. GARRIDO, “Program Refactoring in the Presence of Preprocessor Directives,” *Ph.D. thesis*, 2005.

太骚气了,这篇文章用Maude specification language(类似函数式语言)来定义Cpp的语法和语义.

再去现学Maude的成本太大了,关键最后作者也没有开源自己的成果.

### J. Liebig, S. Apel, C. Lengauer, C. Kästner, and M. Schulze, “An Analysis of the Variability in Forty Preprocessor-Based Software Product Lines,” *ICSE*, pp. 105–114, 2010.

### M. Hafiz, J. Overbey, F. Behrang, and J. Hall, “OpenRefactory/C: An infrastructure for building correct and complex C transformations,” *WRT*, pp. 1–4, 2013.

### J. L. Overbey, F. Behrang, and M. Hafiz, “A foundation for refactoring C with macros,” *FSE*, vol. 16-21-Nove, pp. 75–85, 2014.

### F. Medeiros *et al.*, “Discipline Matters: Refactoring of Preprocessor Directives in the #ifdef Hell,” *TSE*, vol. 44, no. 5, pp. 453–469, 2018.

## 