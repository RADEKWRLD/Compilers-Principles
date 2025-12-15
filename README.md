# TINY+ 语法分析器

基于递归下降法实现的自顶向下语法分析程序，支持 TINY 和 TINY+ 语言。

## 项目简介

本项目是编译原理实验三的实现，包含：
- **词法分析器 (Scanner)**：将源代码转换为 token 流
- **语法分析器 (Parser)**：将 token 流构建为抽象语法树 (AST)

## 支持的语言特性

### TINY 基础语法
| 语法结构 | 示例 |
|---------|------|
| 注释 | `{这是注释}` |
| 读语句 | `read x` |
| 写语句 | `write x` |
| 赋值语句 | `x := 10` |
| if 语句 | `if x<10 then ... else ... end` |
| repeat 语句 | `repeat ... until x=0` |
| 算术运算 | `+`, `-`, `*`, `/` |
| 比较运算 | `<`, `=` |

### TINY+ 扩展语法
| 语法结构 | 示例 |
|---------|------|
| 变量声明 | `int a,b,c;` |
| 数据类型 | `int`, `bool`, `string`, `float`, `double` |
| 浮点数 | `d := 1.25` |
| 字符串 | `s := 'hello'` |
| 布尔值 | `true`, `false` |
| do-while 循环 | `do ... while x>=0` |
| 扩展比较运算 | `>`, `>=`, `<=` |

## 文件结构

```
TINYParser/
├── GLOBALS.H   # 全局类型定义（TokenType, TreeNode 等）
├── SCAN.C/H    # 词法分析器
├── PARSE.C/H   # 语法分析器
├── UTIL.C/H    # 工具函数（打印 token、语法树等）
└── MAIN.C      # 主程序入口
```

## 编译方法

### Windows (使用 GCC)

```bash
cd TINYParser
gcc -x c -o all.exe MAIN.C PARSE.C SCAN.C UTIL.C
```

## 使用方法

### 运行命令

```bash
all.exe <源文件名>
```

**注意**：如果文件名不含扩展名，程序会自动添加 `.tny` 扩展名。

### 示例

```bash
# 运行 TINY 程序
all.exe tiny.txt

# 运行 TINY+ 程序
all.exe tiny1.txt
```

## 示例程序

### TINY 示例 (tiny.txt)

```
{计算阶乘}
read x;
if 0<x then
  fact:=1;
  repeat
    fact:=fact*x;
    x:=x-1
  until x=0;
  write fact
end
```

### TINY+ 示例 (tiny1.txt)

```
{TINY+ 示例程序}
int A,B;
bool C;
string D;
D:= 'scanner';
C:=A + B;
do
  A:=A*2
while A<=D
```

## 输出说明

程序会输出两部分内容：

### 1. 词法分析结果（Token 流）

```
1: {A sample program}
2: read x;
   2: reserved word: read
   2: ID, name= x
   2: ;
```

### 2. 语法树

```
Syntax tree:
  Program
    Read: x
    If
      Op: <
        Const: (int) 0
        Id: x
      Assign to: fact
        Const: (int) 1
```

### 语法树节点类型

| 节点类型 | 说明 |
|---------|------|
| Program | 程序根节点 |
| Decl: type | 变量声明 |
| Read: var | 读语句 |
| Write | 写语句 |
| Assign to: var | 赋值语句 |
| If | 条件语句 |
| Repeat | repeat-until 循环 |
| While | do-while 循环 |
| Op: operator | 运算符 |
| Const: (type) value | 常量 |
| Id: name | 标识符 |

## TINY+ 文法

```
program -> declarations stmt-sequence
declarations -> decl ; declarations | ε
decl -> type-specifier varlist
type-specifier -> int | bool | string | float | double
varlist -> identifier { , identifier }
stmt-sequence -> statement { ; statement }
statement -> if-stmt | repeat-stmt | assign-stmt | read-stmt | write-stmt | while-stmt
if-stmt -> if exp then stmt-sequence [else stmt-sequence] end
repeat-stmt -> repeat stmt-sequence until exp
while-stmt -> do stmt-sequence while exp
assign-stmt -> identifier := exp
read-stmt -> read identifier
write-stmt -> write exp
exp -> simple-exp [comparison-op simple-exp]
comparison-op -> < | = | > | >= | <=
simple-exp -> term { addop term }
addop -> + | -
term -> factor { mulop factor }
mulop -> * | /
factor -> ( exp ) | number | identifier | string
```

## 常见问题

### Q: 编译时出现警告怎么办？
A: 警告不影响程序运行，可以忽略。主要是因为 C 和 C++ 字符串常量类型的差异。

### Q: 文件找不到？
A: 确保源文件在当前目录下，或使用完整路径。

### Q: 如何查看详细的 token 输出？
A: 程序默认开启 `TraceScan = TRUE`，会显示所有识别的 token。
