# 语言模型
### 4.2 Class_6
## 文法与语言
* 求解问题与识别语言
问题 &rarr; (abstract to)符号串的集合;
符号串 &rarr; 句子;问题 &rarr; 句子的集合;
所以,求解问题被抽象为了识别语言.

1.文法
绪论中提及过文法的定义,即:
文法 G 是一个四元组,表示为：**G = (V, T, S, P)**,其中:
* 变量集合 (V):是变量的非空有穷集合. $\forall A \in V$  , $A$ 称为**语法变量**或**非终极符号**.
* 终极符集合 (T)：是终极符的非空有穷集合. $\forall a \in T$ , $a$ 称为**终极符**. $V \cap T = \emptyset$ .
* 开始符号 (S):是开始符号,且 $S \in V$ .
* 产生式集合 (P):是产生式的非空有穷集合.产生式形如 $\alpha \rightarrow \beta$ ,读作“ $\alpha$  定义为 $\beta$ ”. $\alpha \in (V \cup T)^+$ ,且 $\alpha$ 中至少包含一个非终极符号(即 $V$ 中的元素). $\beta \in (V \cup T)^*$ (可以是空串).

2.Chomsky文法分类
3.正则文法
* 定义:一个正则文法 G 是一个四元组,表示为：**G = (V, T, S, P)**,其中:
  * V:非终极符集合
  * T:终结符集合
  * S:文法开始符
  * P:产生式集合,其中产生式形如 $A \to a 或 A \to aB,a\in T,A,B\in V$
* 正则语言:由G生成的语言,即 $L_{(G)}=\lbrace \omega \mid \omega \in T^* \text{且} S\Rightarrow^* \omega\rbrace$
* 正则语言的识别:对于文法G,任给串ω能否被G识别
  * 也就是证明是否有 $S \Rightarrow *\omega$
  * 设 $\omega = a_1a_2a_3...a_n,a_i\in T$,若n:
    * $n = 1$,判断 $S \rightarrow a_1$ 是否在 $P$ 中;
    * $n \geqslant 2$,判断是否有 $S\rightarrow a_1B_1$, 进而证 $B_1 \rightarrow a_2a_3...a_n$是否成立
    * 重复上述过程
  * 算法描述:从右向左推导
    * P中选择右边为串ω的最右字符 $a_n$, 符合条件的产生式左边的非终极符组成一个集合 $V_1$ ,即:
    * $V_1=\lbrace A_1 | A_1 \rightarrow a_n \in P\rbrace$,同理:
    * ...
    * $V_k=\lbrace A_k | A_k \rightarrow a_{n-k+1}A_{k-1} \in P,且A_{k-1}\in V_{k-1}\rbrace$
    * $V_n=...$,检查 $S$ 是否属于 $V_n$ ,若是,则可以由 $S$ 推导出 $\omega$ ,否则不能
  * 伪代码实现
```
for a in w :
  if a not in T : return false

n = w.size()
a_n = w.back()
V.clear()
V.addAll(A: A->a_n in P)
i=2;
while i<=n :
  V0 = V.copy()
  V.clear()
  while V0.notEmpty() :
    A = A: A->{a_n-i+1}B in P and B in V0
    V.addAll(A)
    V0.removeAll(B);
  i++;
```

4.有限自动机
* 组成
  * 输入带:放字符串的装置
  * 有限控制器:包含不同的内部状态
  * 读指针
* 原理
在一定的时间间隔内,自动机根据从输入带上读入的符号和当前的内部状态,进入一个新的状态.
* 特点
  * 字符串输入,输入带传送
  * 只输出是否接受输入
  * 固定中央处理器没有记忆功能
  * 类似于一个语言识别器
* 分类:每次转换后状态是否唯一
  * DFA(确定有限自动机)
  * NFA(非确定有限自动机)
  * 两者存在等价关系,即每一台非确定有限自动机都等价于某一台确定有限自动机
* 语言是正则的,当且仅当它可以被有限自动机接受


$Homework 3:$
1.编写正则语言的识别算法

2.构造一正则文法或有限自动机识别:含有子串011的所有0.1组成的串并给出一实例的识别过程.

### 正则语言的识别算法
``` C++
#include <iostream>
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <string>

using namespace std;

// A -> aB or A -> a
struct Production
{
    char nonTerminal; // nonterminal on the left side
    string rhs;       // right side of the production (terminal or terminal+nonterminal)
};

bool recognizeRegularLanguage(const vector<Production> &grammar,
                              const unordered_set<char> &terminals,
                              char startSymbol,
                              const string &word)
{
    int n = word.size();
    if (n == 0)
    {
        // empty string not accepted
        return false;
    }

    // check if all characters in the word are terminals
    for (char c : word)
    {
        if (terminals.find(c) == terminals.end())
        {
            return false; // illegal character
        }
    }

    vector<unordered_set<char>> V(n + 1);
    // initialize 
    for (const auto &prod : grammar)
    {
        if (prod.rhs.size() == 1 && prod.rhs[0] == word[n - 1])
        {
            V[n - 1].insert(prod.nonTerminal); // A->a_n in P
        }
    }

    // scan the string from right to left
    for (int i = n - 2; i >= 0; i--)
    {
        unordered_set<char> V0 = V[i + 1]; // nonterminals of last step
        for (const auto &prod : grammar)
        {
            if (prod.rhs.size() == 2 && prod.rhs[0] == word[i])
            {
                // (A->a_{n-i+1}B in P) and (B in V0)
                char B = prod.rhs[1];
                if (V0.find(B) != V0.end())
                {
                    V[i].insert(prod.nonTerminal);
                }
            }
        }
    }

    // check if start symbol existed
    return V[0].find(startSymbol) != V[0].end();
}

int main()
{
    // grammar be shaped like A -> aB or A -> a (char -> string)
    vector<Production> grammar = {
        {'S', "aA"},
        {'A', "b"}};
    unordered_set<char> terminals = {'a', 'b'};
    char startSymbol = 'S';

    // test words
    vector<string> testWords = {"ab", "aa", "b", "aab"};

    for (const string &word : testWords)
    {
        bool accepted = recognizeRegularLanguage(grammar, terminals, startSymbol, word);
        cout << "string \"" << word << "\" is "
             << (accepted ? "accepted!" : "refused.") << endl;
    }

    return 0;
}
```

### 正则文法识别子串
正则文法如下:  
文法 *G* 的定义如下:  
$$
\begin{aligned}
G = (&\lbrace S, A, B, C\rbrace, \lbrace 0,1\rbrace, S,\\\\
     &\lbrace S\to0A,S\to1S,\\\\
     & A\to0A,A\to1B, B\to0A,B\to1C,\\\\
     & C\to0C,C\to1C, C\to0,C\to1\rbrace)
\end{aligned}
$$  

具体解释如下:
- $V = \lbrace S, A, B, C\rbrace$
  1. $S$ 表示初始状态
  2. $A$ 表示读到子串0
  3. $B$ 表示读到子串01
  4. $C$ 表示读到串011
- $T = \lbrace 0, 1\rbrace$ 
- $S$  为开始符
- $P$  为产生式集合，具体为：
  1. $S \rightarrow 0A$ 
  2. $S \rightarrow 1S$ 
  3. $A \rightarrow 0A$ 
  4. $A \rightarrow 1B$ 
  5. $B \rightarrow 0A$ 
  6. $B \rightarrow 1C$ 
  7. $C \rightarrow 0C$ 
  8. $C \rightarrow 1C$ 
  9. $C \rightarrow 0$ 
  10. $C \rightarrow 1$ 

实例识别过程:
以串"01011011"为例:
- $a_1 = 0,B_1 = 1011011$ ,由于有 $S \rightarrow 0A$ ,即判断 $B_1 \rightarrow a_2...a_8$ 是否成立.
- $a_2 = 1,B_2 = 011011,A \rightarrow 1B \Rightarrow B2 \rightarrow a_3...a_8$ 
- $a_3 = 0,B_3 = 11011,B \rightarrow 0A \Rightarrow B3 \rightarrow a_4...a_8$ 
- $a_4 = 1,B_4 = 1011,A \rightarrow 1B \Rightarrow B4 \rightarrow a_5...a_8$ 
- $a_5 = 1,B_5 = 011,B \rightarrow 1C \Rightarrow B5 \rightarrow a_6...a_8$ 
- $a_6 = 0,B_6 = 11,C \rightarrow 0C \Rightarrow B6 \rightarrow a_7a_8$ 
- $a_7 = 1,B_7 = 1,C \rightarrow 1C \Rightarrow B7 \rightarrow a_8$ 
- $B_7 = 1$ ,只有一个字符,由于有 $C \rightarrow 1$ ,故识别结果为接受


### 4.9 Class_7

正则语言的描述局限:
* 不能描述配对或嵌套结构
* 不能描述重复串
正则语言泵引理

5.上下文无关语言
* 定义:一个正则文法 G 是一个四元组,表示为：**$G = (V, T, S, P)$**,其中:
  * V:非终极符集合
  * T:终结符集合
  * S:文法开始符
  * P:产生式集合,其中产生式形如 $A \to \beta, A \in V, \beta \in (  V \cup T )^*$
* Chomsky范式:产生式形如 $A \rightarrow BC 或 A \rightarrow a,其中a\in T,A,B,C \in V,且B,C \notin S,允许空产生式S \rightarrow \epsilon $
* 任一上下文无关文法都可以等价转换为Chomsky范式
* 上下文无关语言,上下文无关语言的识别
* 相关定义:
  * $\omega = x_1x_2...x_n$  
  * $\omega _ {ij}=x _ i...x _ {i+j-1}$
  * $V_{ij}=\lbrace A|A\Rightarrow ^*\omega_{ij}\rbrace$  
* 识别算法:
  * $若n=1,判断S \to x_1,是否在P中$
  * $若n\geqslant 2,判断(S \to BC) \in P, 且BC \Rightarrow ^+ \omega,即是否有:B \Rightarrow ^+\omega_{1k},C\Rightarrow ^+\omega _{(k+1)(n-k)}$
  * $求V_{ij}=\lbrace A|(A\to BC)\in P \rbrace, 且B \in V_{ik},C\in V_{(i+k)(j-k)}$
  * $判断S\in V_{1n},即说明\omega 能否被G识别$
* 伪代码:
```
for i:=1 to n :
  V[i][1].clear()
  for p in P.single():
    if p.body == X[i] && X[i] in T:
      V[i][1].add(p.head)

for j:=2 to n :
  for i:=1 to n-j+1 :
    V[i][j].clear;
    for k:=1 to j-1 :
      for p in P.double():
        if p.body.first in V[i][k] && p.body.second in V[i+k][j-k] :
          V[i][j].add(p.head)

return !V[1][n].empty()
```

$Homework 4$  
将下面文法转换为乔姆斯基范式  
    S &rarr; aSbS  
    S &rarr; bSaS  
    S &rarr; e  
并给出一识别实例abaabb的倒三角形求法

### 4.16 Class_8
## 下推自动机
* 下推自动机(PDA)是一种抽象的计算模型
* 下推自动机比有限状态自动机复杂:比自动机多一个长度不受限制的栈.
* PDM定义: $M=(Q,\Gamma,\Sigma,\epsilon,\delta,q_0,Z_0,F)$
  * $Q:有限状态集$
  * $\Gamma :栈符号集$
  * $\Sigma :输入符号集$
  * $q_0 \in Q :初始状态$
  * $Z_0 :栈初始符号$
  * $F \subseteq Q:终止状态集$
  * $\delta :转换函数$
* 确定/非确定 PDA
  * 确定： $Q \times (\Sigma \times \Gamma^\ast) \to Q \times \Gamma ^\ast$
  * 非确定： $Q \times (\Sigma \cup \lbrace\epsilon\rbrace \times \Gamma ^\ast) \to Q \times \Gamma ^\ast$
  * 确定 PDA 与 非确定 PDA 是等价的  

* 初始格局: $(q_0,\omega ,Z_0)$
* 接受格局:
  * 空栈接受: $(q,\epsilon,\epsilon)$
  * 空栈接受: $(q,\epsilon,\gamma),\gamma \in \Gamma ^*$
  * 对于确定PDA,两种接受不等价,但对于非确定PDA是等价的

## 正则语言可计算
* P类问题: $P= ⋃_kTIME(n^k)$
  * 是单带DTM在多项式时间内可判定的语言类
  * 所有与单带DTM多项式时间等价的计算模型,P类是稳定不变的
  * 对应计算机上实际可解的问题类
* NP类问题: $NP = ⋃_kUTIME(n^k)$,其中 $UTIME(t_{(n)})$ 时在NTM在 $O(t_{(n)})$ 时间内可判定的语言集合
* NPC:若NP中的每个语言A都多项式时间归约到B,则B是NP难的;若B又属于NP, 则B是NP完全的
