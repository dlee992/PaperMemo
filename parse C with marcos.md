# 如何直接对带宏的C源代码进行解析

1. 先要明确，我希望根据`C source code`得到什么东西？

2. **text processing feature**

3. **#ifdef** is crucial when creating header files. This macro is used in order to make sure a header file is only included once

4. ```c
   #ifndef MACROS_IN_C_H	
   #define MACROS_IN_C_H	
   
   /* Function prototypes */	
   
   #endif // MACROS_IN_C_H
   ```

5. From the preprocessor’s perspective, a file consists of `parts`. A part is either a `conditional compilation construct` (`#if` or `#ifdef`), a `control line` (`#define` or `#error`), or a `text line` (a line not beginning with `#`).

6. 有点越看越迷茫的感觉。。。。。

7. 

8. 首先要得到一个尽可能完整的`AST`，然后对这个AST上的每个节点做尽可能完善的`type annotation`

   1. `Type annotation`: a variable, function, pointer (all about type?)









## 直接用C编译工具去解析，多遍parse是否能够补全类型信息

1. 什么叫多次parse？根据类型推导规则，多次遍历一点点生成完整的类型信息？

## 直接用Ocamllex和yacc去编写自己的parser

1. 问题1：`Ocaml parser`能够解析到何种精读？特别是如何处理源码中的宏？

2. 问题0：宏的常见用法有几种？具体是哪些？

   1. #include, #define, #if,  #elif, #endif, #else, #ifndef, #if defined

   2. #include <header.h>

   3. 定义字面常量

   4. 条件编译

   5. 简单的函数多态

   6. Pre_defined Marcos: `__DATE__`, `__FILE__`, `__LINE__`, `__STDC__`, `__TIME__`.

   7. ```c
      #define VALUE 5
      
      #ifndef _CRTAPI1
      #define _CRTAPI1 __cdecl
      #endif
      #ifndef _CRTAPI2
      #define _CRTAPI2 __cdecl
      #endif
      
      #ifdef UNICODE
      int getValueW(wchar_t *p);
      #define getValue getValueW
      #else
      int getValueA(char *p);
      #define getValue getValueA
      #endif
      
      #define max(a,b) ((a)>(b)?(a):(b))
      ```

