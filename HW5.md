# HW5

2020K8009915008 林孟颖

## 练习4.1.1：

考虑上下文无关文法
	S→ S S + | S S * | a
    以及串 aa+a*

	1) 给出这个串的一个最左推导
	2) 给出这个串的一个最右推导
	3) 给出这个串的一颗语法分析树
	4) 这个文法是否是二义性的？ 证明你的回答（选作）
	5) 这个文法生成的语言是什么？

---

1) 
   $$
   \begin{align}
   S 
   & \Rightarrow SS*\\
   & \Rightarrow SS+S*\\
   & \Rightarrow aS+S* \\
   & \Rightarrow aa+S* \\
   & \Rightarrow aa+a* \\
   \end{align}
   $$

2) 
   $$
   \begin{align}
   S 
   & \Rightarrow SS*\\
   & \Rightarrow Sa*\\
   & \Rightarrow SS+a*\\
   & \Rightarrow Sa+a* \\
   & \Rightarrow aa+a* \\
   \end{align}
   $$

3) ```
       	   S
            	______|______
          	|        |   |
          	S    	 S    *
   ____|____    |
   |   |   |    |
   S   S   +    a
   |   |       
   a   a    
   
   ```

4) 否。若存在多个最左派生或多个最右派生，则该文法具有二义性，下证对于该文法生成的任意串，其最左/右派生唯一。先证明其最左派生唯一：

   - 假设该串由该文法1步派生而来，即该串为`a`，最左派生过程为`S->a`，唯一；

   - 假设该串由该文法k（k>=1）步派生而来时，其最左派生唯一，则当串$s$由k+1步派生得来时，不妨设其以+结尾，则其派生的第一步一定为：
     $$
     S\Rightarrow S_1S_2+
     $$
     其中$S_1$和$S_2$各自派生出长度小于等于k的串$s_1$和$s_2$，二者的最左派生唯一，故$s$的最左派生唯一。当其以*结尾，同理可证。

   - 故对于由该文法经过任意步派生得到的串，其最左派生唯一

   用相同方法可证明其最右派生唯一。综上，该文法不存在二义性。

5) $L(S)=a(a+|a*)^*$，即该语言是字符a和运算符+、*组成的后缀表达式。



## 练习4.1.2：

设计文法表示下面的串组成的语言：由二元运算符 ⊕、⊗ 和变量 x 组成的表达式，运算符右结合且 ⊕ 的优先级高于 ⊗。
$$
\begin{align}
& E → T ⊗ E | T\\
& T → F ⊕ T | F\\
& F → ( E ) | x\\
\end{align}
$$

> [编译原理（三）语法分析：3.二义性与二义性的消除_消除二义性-CSDN博客](https://blog.csdn.net/sandalphon4869/article/details/103423292#:~:text=对于 A→αAβ ，若 A （左右都有的非终结符）在终结符（即终结符在β中）左边出现，则A产生式具有左结合性质。 如： E →,-F 是右结合（ F 即 A ， - 是终结符）)

## 练习4.1.3：

下面是一个只包含符号 a 和 b 的正则表达式的文法，其中用 + 替代表示并运算的字符 | ，以避免和文法中作为元符号使用竖线混淆

```
	rexpr→rexpr + rterm | rterm
	rterm→rterm rfactor | rfactor
	rfactor→ rfactor* | rprimary
	rprimary→ a | b
```



1) 对该文法提取左公因子
2) 提取左公因子的变换能使这个文法适用于自顶向下的语法分析技术吗？
3) 将提取了左公因子的文法继续消除左递归
4) 此时得到的文法适用于自顶向下的语法分析吗？

---

1. 该文法的每一个产生式都不含左公因子；

2. 不能，其存在左递归，如`rterm→rterm rfactor`

3. ```
   rexpr -> rterm rexpr'
   rexpr' -> + rterm rexpr' | ε
   rterm -> rfactor rterm'
   rterm' -> rfactor rterm' | ε
   rfactor -> rprimary rfactor'
   rfactor' -> * rfactor' | ε
   rprimary -> a | b
   ```

4. 已消除左递归，适用





