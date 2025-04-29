# 计算模型
### 2.26 Class_1
## 图灵机模型
1. 图灵机组成
* 两端无限的线性带(读写介质)
* 有限的符号表(表示信息)
* 有限的信息处理状态
* 信息处理动作
* 信息处理方法/规则

2. 形式化定义
一个图灵机(Turing Machine)可以七元组形式化定义为：
* **M = (Q, ∑, Γ, δ, B, q₀, F)**,其中:
* Q(状态集合): 状态的有限集合.  
* ∑(输入符号集): 有限字母表,表示输入符号的集合.  
* Γ(带符号集): 线性带符号的集合,满足 **∑ ⊆ Γ**.  
* B(空白符): 特殊符号,满足 **B ∈ Γ** 且 **B ∉ ∑**.  
* q₀(初始状态): 初始状态,满足 **q₀ ∈ Q**.  
* F(终止状态集): 终止状态的集合,满足 **F ⊆ Q**.  
* δ(转移函数):  **δ: Q × Γ → Q × (Γ × {L, R, S})** 的函数,定义状态和符号的转移规则(`L` 左移,`R` 右移,`S` 保持不动).

### 3.5 Class_2
$Rome Number Title:example$  
I. $\delta(q, a) = (p, (b, L))$
```    
if(status == q) then {
    read << a;
    convert(δ : (q, a) → (p, b));
    read --;
}
//若当前状态为q,读写头读取a,经过δ转换后,图灵机状态改为p,
//线性带上改变为b,同时读写头左移一格
```
II. $\delta(q, a) = (p, (a, R))$   
```
if(status == q) then {
    read << a;
    convert(δ : (q, a) → (p, a));
    read ++;
}
//若当前状态为q,读写头读取a,经过δ转换后,图灵机状态改为p,
//线性带上a不改变,同时读写头右移一格
```

III. $\delta(q, a) = (q, (B, S))$
```
if(status == q) then {
    read << a;
    convert(δ : (q, a) → (q, B)); // B.isEmpty() == true
    read ;
}
//若当前状态为q,读写头读取a,经过δ转换后,图灵机状态不改变,
//线性带上a被清空,同时读写头不动
```

IV. 有图灵机 $M=(\{q_0,q_1,q_2\},\{0,1\},\{0,1,B\},\delta,q_0,B,\{q_2\})$ ,
其中: $\delta(q_0,0)=(q_0,0,R)$ , $\delta(q_0,1)=(q_1,1,R)$ , $\delta(q_1,0)=(q_1,0,R)$ , $\delta(q_1,B)=(q_2,2,R)$  
表达方法:状态转移图/表
以表为例,
| $\delta$     | 0           | 1           | B           |
| ------------ | ----------- | ----------- | ----------- |
| $q_0$        | ($q_0,0,R$) | ($q_1,1,R$) |
| $q_1$        | ($q_1,0,R$) |             | ($q_2,B,R$) |
| $\Delta q_2$ |             |             |

可以分析出本图灵机作用为识别由0和1组成的且只含有一个1的字符串

V.设计一台图灵机,接受由0和1组成的,且0与1出现次数相同,0先出现的字符串形如0...01...1
* 基本思路：读头将第一个0改为x,右移,把找到的第一个1改为y,然后退回去直到遇到第一个x,再右移把遇到的第一个0改为x,右移,把找到的第一个1改为y,如此反复直至指针指向空白B为止.
* 实现步骤:
  * 设计状态数,符号.初始状态和终止状态
  * 绘制状态转移图,得到转移函数

3. 格局:当前状态,当前带内容和读写头位置组成的,对机器状态的表示
    设计格局包括初始格局,终止格局和格局转换(C1产生C2,记作C1├ C2) 
    ┣*:表示转换关系的自反/传递闭包,即多步转换
    
4. 计算: $q_0ω┣*xq_fy$ 称为称为一个以ω为输入,xy为输出的计算,即
    计算是从初始格局到终止格局按照动作函数规定的规则进行的一系列转换的格局转换序列.
    计算的长度为格局转换个数.

VI.设有图灵机 $M =(\{q_0,q_1\}, \{0,1\}, \{0,1,B\}, δ, q_0, B, f)$, 其中转换函数δ定义为：
<figure>

$δ(q_0,0) = (q_1,(0,R))$, $δ(q_0,1) = (q_1,(1,R))$,
$δ(q_0,B) = (q_1,(B,R))$, $δ(q_1,0) = (q_0,0,L)$, 
$δ(q_1,1) = (q_0,1,L)$, $δ(q_1,B) = (q_0,B,L)$.

</figure>

本例说明了并不是所有输入都"合法".如输入01,10,执行永远无法到达终点

应用实例:自然数计算,串的拷贝等

$Homework 1:$

1.设计一台图灵机,接受由0和1组成的,且0与1出现次数相同的字符串.

2.设计图灵机识别字符串形如w#w,其中w是由0,1构成的字符串.

要求:
* 给出图灵机的设计思路
* 给出图灵机定义
* 给出一个长度大于5的字符串的识别过程
  

### 1.设计图灵机接受0和1出现次数相同的字符串

#### 设计思路：
* 由0开头的,接受0和1出现次数相同的形如"00...01...11"字符串的设计例5已经给出
* 本题目的区别在于01出现位置不一定,但思路类似,即 __接受开头的0/1,然后寻找匹配的1/0,找到后回溯__ 
* 对各状态的解释:
  * $q_0$:起始/XY的数量相当,等待新的匹配/等待截止
  * $q_1$:(子串0开头时)表示0冗余,等待找到右侧的1
  * $q_2$:(子串0开头时)表示正在回溯,等待找到左侧的X
  * $q_3$:(子串1开头时)表示1冗余,等待找到右侧的0
  * $q_4$:(子串1开头时)表示正在回溯,等待找到左侧的Y
  * $q_5$:终止态
  
#### 图灵机定义:
$M =(\{q_0,q_1,q_2,q_3,q_4,q_5\}, \{0,1\}, \{0,1,X,Y,B\}, δ, q_0, B, \{q_5\})$,其中:

| $\delta$ | 0           | 1           | X           | Y           | B           |
| -------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| $q_0$    | ($q_1,X,R$) | ($q_3,Y,R$) | ($q_0,X,R$) | ($q_0,Y,R$) | ($q_5,B,S$) |
| $q_1$    | ($q_1,0,R$) | ($q_2,Y,L$) | -           | ($q_1,Y,R$) | -           |
| $q_2$    | ($q_2,0,L$) | -           | ($q_0,X,R$) | ($q_2,Y,L$) | -           |
| $q_3$    | ($q_4,X,L$) | ($q_3,1,R$) | ($q_3,X,R$) | -           | -           |
| $q_4$    | -           | ($q_4,1,L$) | ($q_4,X,L$) | ($q_0,Y,R$) | -           |
| $q_5$    | -           | -           | -           | -           | -           |

#### 识别过程示例:
以串"100011"为例:
$q_0100011 $
$┣ \space Yq_300011 \space $
$┣ \space q_4YX0011 \space ┣ \space Yq_0X0011  \space ┣ \space YXq_00011  \space $
$┣ \space YXXq_1011 \space ┣ \space YXX0q_111  \space ┣ \space YXXq_20Y1$
$┣ \space YXq_2X0Y1 \space ┣ \space YXXq_00Y1  \space $
$┣ \space YXXXq_1Y1 \space ┣ \space YXXXYq_11  \space $
$┣ \space YXXXq_2YY \space ┣ \space YXXq_2XYY  \space ┣ \space YXXXq_0YY  \space ┣ \space YXXXYq_0Y  \space $
$┣ \space YXXXYYq_0B$
又以串"1000111"为例:
其他同上,到 $┣ \space YXXXYYq_01 \space ┣ \space YXXXYYq_3B$ 后拒绝停机

### 2.设计图灵机识别字符串形如w#w,其中w是由0,1构成的字符串
#### 设计思路：
* 先匹配并标记第一个0/1,设置状态用于判断右侧要匹配0还是1
* 右移至 '#' 后第一个未标记的0/1,若配对成功则将其标记,然后开始左;若配对失败则拒绝停机
* 左移至第一个已标记的0/1,然后右移
* 此时,相当于到达了第一个未标记0/1,重复上述流程,直至停机或左移到B终止
* 因此,需要以下的状态位:
  * 用于记录在 '#' 左右
  * 用于记录需要匹配的0/1
  * 用于记录回溯历程
  * 用于记录是否终止
* 状态如下:
  * $q_0$:起始状态
  * $q_1$:在#左侧,待匹配0
  * $q_2$:在#左侧,待匹配1
  * $q_3$:在#右侧,待匹配0
  * $q_4$:在#右侧,待匹配1
  * $q_5$:#左侧回溯状态
  * $q_6$:#右侧回溯状态
  * $q_7$:终止状态,当且仅当处于$q_0$时输入为'#'时接受识别

#### 图灵机定义:  

$$\begin{aligned}
M  =&(\{q_0,q_1,q_2,q_3,q_4,q_5,q_6,q_7\},\newline 
 &\{0,1,\\#\}, \{0,1,\\#,T,B\}, δ, q_0, B, \{q_7\})
 \end{aligned},其中:$$  

| $\delta$ | 0           | 1           | #             | T           | B   |
| -------- | ----------- | ----------- | ------------- | ----------- | --- |
| $q_0$    | ($q_1,T,R$) | ($q_2,T,R$) | ($q_7$,#, $S$) | -           | -   |
| $q_1$    | ($q_1,0,R$) | ($q_1,1,R$) | ($q_3$,#, $R$) | -           | -   |
| $q_2$    | ($q_2,0,R$) | ($q_2,1,R$) | ($q_4$,#, $R$) | -           | -   |
| $q_3$    | ($q_6,T,L$) | -           | -             | ($q_3,T,R$) | -   |
| $q_4$    | -           | ($q_6,T,L$) | -             | ($q_4,T,R$) | -   |
| $q_5$    | ($q_5,0,L$) | ($q_5,1,L$) | -             | ($q_0,T,R$) | -   |
| $q_6$    | -           | -           | ($q_5$,#, $L$) | ($q_6,T,L$) | -   |
| $q_7$    | -           | -           | -             | -           | -   |

#### 识别过程示例
以串"010#010"为例:
$q_0010 \\# 010 $
$┣ \space Tq_110 \\# 010 \space $
$┣^* \space T10 \\# q_3010 \space ┣ \space T10q_6 \\# T10  \space $
$┣^* \space Tq_010 \\# T10 \space ┣ \space TTq_20 \\# T10  \space $
$┣^* \space TT0 \\# Tq_410 \space ┣ \space TT0 \\# q_6TT0  \space $
$┣^* \space TTq_00 \\# TT0 \space ┣ \space TTTq_1 \\# TT0  \space $
$┣^* \space TTT \\# TTq_30 \space ┣ \space TTT \\# Tq_6TT  \space $
$┣^* \space TTq_5T \\# TTT \space ┣ \space TTTq_0 \\# TTT  \space $
$┣ \space TTTq_7 \\# TTT \space $

### 3.12 Class_3

5. 图灵机的变形:
* 多带图灵机
* 非确定图灵机
* 多指针图灵机
* 多维图灵机
* 离线图灵机

语言: $L(M) = \{ω|q_0ω┣*xq_fy\}$ 称为M识别的语言.即图灵机M能够接受停机的所有输⼊信息串的集合就是M能识别的语言.

* 可识别(可递归枚举):如果有图灵机识别⼀个语言,则称该语⾔是图灵可识别的
* 可判定:如果有图灵机对所有输⼊都停机,则称该语言图灵可判定
* 图灵可判定语⾔都是图灵可识别的,但图灵可识别的不都是图灵可判定的
* 语言图灵可识别,当且仅当有图灵机识别它
* 语言可判定,当且仅当有图灵机判定它

通用图灵机:接受任意一台图灵机M的编码,并能够模拟M的运作
任意一台图灵机都可以等价转化为一台通用图灵机
与图灵机等价的计算模型:寄存器机,Lambda演算

Church－Turning论题：
⼀切直觉上能⾏可计算的函数都可用图灵机计算,反之亦然

## 寄存器机

相关概念
* 寄存器机:
  * 与图灵机等价
  * 介于图灵机与实际数字计算机之间的一种抽象机
  * 用于分析算法效率和与机器代码相关的研究
* 一般结构:
  * 寄存器
  * 指令集
  * 控制器
  * 输入输出带
  * 指令标号
* 典型寄存器机:
  * 计数器机,指针机
  * RAM机(Random Access Machine)
  * RASP机(Random Access Stored Program Machine)

1.RAM机
* 哈佛结构(将程序指令存储和数据存储分开)
* 允许间接寻址(当然也可以直接寻址)
* 指令：
  * 格式:指令 操作数
  * 指令分类：
    * 寄存器存 LOAD
    * 寄存器取 STORE
    * 跳转 JUMP  JGTZ  JZERO 
    * 运算 ADD  SUB  MULT   DIV
    * 输⼊输出 READ  WRITE
    * 结束 HALT
  * 操作数：
    * i表示R<sub>i</sub>寄存器的内容,记为C(i)；
    * *i表示间接寻址,操作数的地址是R<sub>i</sub> 内容,j=C(i),即其值是C(C(i)),若j<0则停机;
    * =i表示立即数i.
  * 操作数映射V(a):
    * $V_{(i)}=C_{(i)}$
    * $V_{(*i)}=C_{C_{(i)}}$
    * $V_{(=i)}=i$
  * C(0)起到临时存储计算值的作用
* 初始状态
  * C(i) = 0,对任意 i ≥ 0;
  * 位置计数器指向程序P的第⼀条指令;
  * 输⼊串放在输⼊带上,读头指向输⼊串的第⼀个符号;
  * 输出带为空
* RAM程序P:指令/伪指令的有限序列,以HALT结尾,表征输入到输出的函数 $f(x_1, …, x_n) = (y_1, …, y_m)$

例:输⼊ $X_1X_2…X_i…X_n0$ ,其中 $X_i$ 为1或2,判断1和2出现的个数是否相同?
思路:读1加一,读2减一,若读0时结果为0则相等
程序:
```
# r0:累加器 r1:存放读取字符 r2:存放差值

        LOAD =0    # clear accumulator
        STORE 2    # clear diff-register
        READ 1     # read first char
REPEAT: LOAD 1     # char to accumlator
        JZERO END  # end condition
        SUB 1      
        JZERO ONE  # jump if read 1
        LOAD 2     # here means read 2
        SUB 1
        STORE 2    # diff-register--
        JMP NEXT
ONE:    LOAD 2     # here means read 1
        ADD 1      
        STORE 2    # diff-register++
NEXT:   READ 1     # read next char
        JMP REPEAT   
END:    LOAD 2     # check diff-register
        JZERO EQUAL# jump if number of 1 and 2 are the same
        WRITE =0   # print result
        HALT
EQUAL:  WRITE =1   # print result
        HALT         
```

### 3.19 Class_4

2.RASP机
* 冯诺依曼结构
* 程序存储在寄存器中,可修改
* 不允许间接寻址,指令集与RAM机同
* 每条指令占两个寄存器,第⼀个存放操作码,第⼆个存放操作地址,操作码在寄存器中表现为数值而非符号
* 定理:任意一个RASP程序PS,均有等价的RAM程序P;反之亦然
* 定理:RAM机和RASP机计算能力等价

## Lambda演算模型
1.Lambda演算理论

__相关概念__:
* Lambda演算理论是⼀个形式系统,同Turing机⼀样可作为计算模型
* Lambda演算理论是函数式语⾔的基础,也是指称语义学的理论基础,可用于理论证明
* Lambda演算形式系统主要有两部分:符号系统-表达式 和 符号变换规则


__Lambda表达式定义__:

Lambda表达式(λ表达式)由变量名,抽象符号 `λ` `.` 以及括号等符号构成.用 `E` 表示λ表达式,定义如下:
* 变量 : 如果 `x` 是变量名,则 `x` 是λ表达式.
* 应用型表达式 : 如果 `E1` 和 `E2` 是λ表达式,则 `E1 E2` 是λ表达式.
* 抽象型表达式 : 如果 `E` 是λ表达式,`x` 是变量,则 `λx.E` 是λ表达式.
* 括号表达式 : 如果 `E` 是λ表达式,则 `(E)` 是λ表达式.

__形式化表述(BNF)__:

```
E ::= x       // 变量
    | E E     // 应用型表达式
    | λx.E    // 抽象型表达式
    | (E)     // 括号表达式
```

__记法约定__:

* 应用型表达式 :
  $E_1 \, E_2 \, E_3 \, \dots \, E_n \equiv (\dots((E_1 \, E_2) \, E_3) \dots) \, E_n$
* 抽象型表达式 :
  $\lambda x_1. \, \lambda x_2. \, \dots \, \lambda x_n. \, E \equiv \lambda x_1. \, (\dots (\lambda x_n. \, E) \dots)$
* 多参数抽象型表达式: 
  $\lambda x_1. \, \lambda x_2. \, \dots \, \lambda x_n. \, E \equiv \lambda x_1 x_2 \dots x_n. \, E$
* 抽象型表达式与应用型表达式结合:  
  $\lambda x_1. \, \lambda x_2. \, \dots \, \lambda x_n. \, E_1 \, E_2 \, \dots \, E_n \equiv \lambda x_1. \, \lambda x_2. \, \dots \, \lambda x_n. \, (E_1 \, E_2 \, \dots \, E_n)$

__相关定义__:
* 作用域:对于 $\lambda x . E$, x 的作用域是 E
* 约束出现与自由出现:
  * 变量 x 在表达式 $\lambda x . E$的出现为约束出现
  * 当变量 x 出现的位置不被任一表达式约束时, x 的出现为自由出现
* 自由变量: x 在 E 中自由出现时, x 是 E 中的自由变量
  * 用 $fv_{(E)}$ 表示E中所有自由变量集
  * $fv_{(x)} = \{x\}$
  * $fv_{(E_1 E_2)} = fv_{(E_1)} \cup fv_{(E_2)}$
  * $fv_{(\lambda x . E)} = fv_{(E)} - \{x\}$
  * $fv_{((E))} = fv_{(E)}$
* 替换: $E[E_0/x]$ :将 E 中 x 的自由出现用 E<sub>0</sub> 替代.  
  * $E \equiv x$  :  $x[E_0/x] = E_0$  
  * $E \equiv y$ 且  $x \neq y$ : $y[E_0/x] = y$  
  * $E \equiv E_1,E_2$ : $(E_1 \, E_2)[E_0/x] = (E_1[E_0/x]) \, (E_2[E_0/x])$  
  * $E \equiv \lambda x. E'$ : $(\lambda x. E')[E_0/x] = \lambda x. E'$  
  * $E \equiv \lambda y. E'$ ,且 $x \neq y $,若 $x \in fv(E')$ :
    * $y \in fv(E_0)$ : $(\lambda y. E')[E_0/x] = \lambda z. E'[z/y][E_0/x]$ 
    * 否则: $(\lambda y. E')[E_0/x] = \lambda y. E'[E_0/x]$ 

__变换规则__:
* α变换(换名规则): $\lambda x . E \rightarrow _\alpha \lambda y . E[y/x] ,其中 x \not = y 且 $y \notin fv_{(E)}$  
* β变换(应用规则): $(\lambda x . E)E_0 \rightarrow _\beta E[E_0/x]$  
* η变换(保值变换): $(\lambda x . M x) \rightarrow _\eta M ,其中 x \notin fv_{(M)}$  

__归约__:
* 归约基:__β变换和η变换__ 的左部
* Lambda归约:将归约基按规则变换
* 范式: __无归约基__ 的Lambda表达式
* 一个Lambda有范式当且仅当其可以经过有限次变换归约为范式
* 标准规约(最左归约):依据归约基中 λ 符号出现的顺序进⾏的归约
* Church-Rosser 定理: Lambda表达式若有范式,则范式唯一且可由标准归约得到


$Homework 2:$
1.归约下面的λ表达式:
* $(λx.xz)(λy.y)$
* $(λx.xy)(λx.xu)$

2.编程实现
输入λ表达式,输出该表达式的自由名集

### 表达式归约  

$$\begin{aligned}
(λx.xz)(λy.y) &\rightarrow _\beta  (λy.y)z
\newline &\rightarrow _\beta z
\end{aligned}$$ 

$$\begin{aligned}
(λx.xy)(λx.xu) &\rightarrow _\beta  (λx.xu)y
\newline &\rightarrow _\beta  yu
\end{aligned}$$ 

### 编程实现
``` C++
/* 
输入格式: 一行字符串表示一个lambda表达式,用!表示lambda,区分大小写,变量名为单个字符,允许空格
输出格式: 一行字符串表示该lambda表达式中的自由变量，按字典序排列，用空格分隔
*/

#include <bits/stdc++.h>
using namespace std;

enum TokenType
{
    VAR,
    LAMBDA, // 用 ! 表示
    DOT,
    LPAREN,
    RPAREN,
    END
};

struct Token
{
    TokenType type;
    char value; // 仅VAR有效
    Token(TokenType t, char v = '\0') : type(t), value(v) {}
};

struct Expr
{
    virtual ~Expr() = default;
    virtual set<char> freeVars() const = 0;
};

struct Var : Expr
{
    char name;
    explicit Var(char n) : name(n) {}
    set<char> freeVars() const override
    {
        return {name};
    }
};

struct Application : Expr
{
    unique_ptr<Expr> left;
    unique_ptr<Expr> right;
    Application(unique_ptr<Expr> l, unique_ptr<Expr> r)
        : left(move(l)), right(move(r)) {}
    set<char> freeVars() const override
    {
        auto leftVars = left->freeVars();
        auto rightVars = right->freeVars();
        leftVars.insert(rightVars.begin(), rightVars.end());
        return leftVars;
    }
};

struct Abstraction : Expr
{
    char var;
    unique_ptr<Expr> body;
    Abstraction(char v, unique_ptr<Expr> b)
        : var(v), body(move(b)) {}
    set<char> freeVars() const override
    {
        auto vars = body->freeVars();
        vars.erase(var);
        return vars;
    }
};

class Lexer
{
public:
    explicit Lexer(const string &input) : input(input), pos(0) {}
    Token nextToken()
    {
        while (pos < input.size() && isspace(input[pos]))
        {
            ++pos;
        }
        if (pos >= input.size())
        {
            return Token(END);
        }
        char c = input[pos++];
        if (isalpha(c))
        {
            return Token(VAR, c);
        }
        else if (c == '!')
        {
            return Token(LAMBDA);
        }
        else if (c == '.')
        {
            return Token(DOT);
        }
        else if (c == '(')
        {
            return Token(LPAREN);
        }
        else if (c == ')')
        {
            return Token(RPAREN);
        }
        else
        {
            throw runtime_error("Invalid character: " + string(1, c));
        }
    }

private:
    const string &input;
    size_t pos;
};

class Parser
{
public:
    explicit Parser(const string &input) : lexer(input), currentToken(lexer.nextToken()) {}
    unique_ptr<Expr> parse()
    {
        auto expr = parse_E();
        if (currentToken.type != END)
        {
            throw runtime_error("Unexpected token at end of input");
        }
        return expr;
    }

private:
    Lexer lexer;
    Token currentToken;

    void consume(TokenType expected)
    {
        if (currentToken.type == expected)
        {
            currentToken = lexer.nextToken();
        }
        else
        {
            throw runtime_error("Unexpected token");
        }
    }

    unique_ptr<Expr> parse_P()
    {
        if (currentToken.type == VAR)
        {
            char var = currentToken.value;
            consume(VAR);
            return make_unique<Var>(var);
        }
        else if (currentToken.type == LAMBDA)
        {
            consume(LAMBDA);
            if (currentToken.type != VAR)
            {
                throw runtime_error("Expected variable after lambda");
            }
            char var = currentToken.value;
            consume(VAR);
            consume(DOT);
            auto body = parse_E();
            return make_unique<Abstraction>(var, move(body));
        }
        else if (currentToken.type == LPAREN)
        {
            consume(LPAREN);
            auto expr = parse_E();
            consume(RPAREN);
            return expr;
        }
        else
        {
            throw runtime_error("Invalid expression");
        }
    }

    unique_ptr<Expr> parse_E()
    {
        auto left = parse_P();
        while (currentToken.type == VAR || currentToken.type == LAMBDA || currentToken.type == LPAREN)
        {
            auto right = parse_P();
            left = make_unique<Application>(move(left), move(right));
        }
        return left;
    }
};

int main()
{
    string input;
    getline(cin, input);
    try
    {
        Parser parser(input);
        auto expr = parser.parse();
        auto freeVars = expr->freeVars();
        if (freeVars.empty())
        {
            cout << endl;
        }
        else
        {
            vector<char> sortedVars(freeVars.begin(), freeVars.end());
            sort(sortedVars.begin(), sortedVars.end());
            for (size_t i = 0; i < sortedVars.size(); ++i)
            {
                cout << sortedVars[i];
                if (i < sortedVars.size() - 1)
                {
                    cout << " ";
                }
            }
            cout << endl;
        }
    }
    catch (const exception &e)
    {
        cerr << "Error: " << e.what() << endl;
        return 1;
    }
    return 0;
}
```

### 3.26 Class_5

2.纯Lambda演算实例
* 自然数运算
  * 整数定义: $\lambda ab. a(a(...(ab)...)) = \lambda ab. a^nb$
    * $0=\lambda ab.b$
    * $n=\lambda ab.a^nb$
  * 运算:
    * 加法
      * $plus\space m\space n ::= m + n$
      * $plus = \lambda xy.\lambda ab.(x \space a)((y \space a)b)$
      * $plus\space m\space n  = \lambda ab.(\lambda ab.a^mb \space a)((\lambda ab.a^nb \space a)b)$
      $=\lambda ab.(\lambda b.a^mb)(a^nb)$
      $=\lambda ab.a^{m+n}b$ 
    * 乘法:
      * $time\space m\space n ::= m * n$
      * $time = \lambda xy.\lambda a.x \space (y \space a)$
      * $time = \lambda a.\lambda ab.a^mb \space (\lambda ab.a^nb \space a)$
      $=\lambda a.\lambda b.(\lambda b.a^nb)^mb$
      $=\lambda ab.(\lambda b.a^nb)(...(\lambda b.a^nb)b...)$
      $=\lambda ab.a^{mn}b$ 
* 逻辑运算
  * bool值
    * $T::=\lambda ab.a$
    * $F::=\lambda ab.b$
  * bool运算
    * $and::=\lambda xy.x\space y\space F$
    * $or::=\lambda xy.x\space T\space y$
    * $not::=\lambda x.x\space F\space T$
* 关系运算
  * $ISZERO \space x::=\lambda x.x(\lambda a.F)T$
  * $MINUS \space m \space n ::= ...$
  * $LE ::= \lambda m \space n.ISZERO(MINUS \space m \space n)$
  * $EQ ::= \lambda mn.and(LE \space m \space n)(LE \space n \space m)$
  * $LT ::= \lambda mn.and(LE \space m \space n)(not(EQ \space m \space n))$