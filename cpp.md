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



## 已有论文

### P. A. Darnell and P. E. Margolis, “The C Preprocessor,” 1991.

已阅. cpp的存在有历史必然性,主要是通过hard-code的方式来解决C语言在设计之初未能妥善处理的issues.

然而,时至今日,却给C代码的重构带来了很大的困难.

### M. D. Ernst, G. J. Badros, and D. Notkin, “An Empirical Analysis of C Preprocessor Use Coping with the Preprocessor,” *TOSEM*, pp. 1–34, 2000.

实证研究:分析多个应用软件包中对directives和marcos的使用情况,它的实验设置需要细细研究一下.

粗度了一下,感觉比较中规中矩?文章里给出的统计图标还是值得简单看一看,了解一下.

### A. Garrido and R. Johnson, “Refactoring C with Conditional Compilation,” *ASE short*, 2003.

### A. GARRIDO, “Program Refactoring in the Presence of Preprocessor Directives,” *Ph.D. thesis*, 2005.



### J. Liebig, S. Apel, C. Lengauer, C. Kästner, and M. Schulze, “An Analysis of the Variability in Forty Preprocessor-Based Software Product Lines,” *ICSE*, pp. 105–114, 2010.

### M. Hafiz, J. Overbey, F. Behrang, and J. Hall, “OpenRefactory/C: An infrastructure for building correct and complex C transformations,” *WRT*, pp. 1–4, 2013.

### J. L. Overbey, F. Behrang, and M. Hafiz, “A foundation for refactoring C with macros,” *FSE*, vol. 16-21-Nove, pp. 75–85, 2014.

## 