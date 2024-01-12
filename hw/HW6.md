# HW 6

> 知识点：
>
> - 文法分析回溯，LL(1)文法
> - FIRST, FOLLOW集
> - LL(1)文法分析表和分析动作（结合栈）

## 练习4.2.1

使得文法的预测分析产生回溯的原因是什么？仅使用FIRST集合可以避免回溯吗？为什么？

---

原因：文法非LL(1)文法；

仅使用FIRST集合不可以避免回溯，对于含产生式`` A->α|β``的文法，其为LL(1)文法的其中一个条件为：当`ε∈FIRST(β)`，还需要有`FOLLOW(A)` 和`FIRST(a)` 不相交，故仅使用FIRST集合不足以避免回溯。

> 判断LL(1)文法的条件：
>
> 对于任意产生两个不同产生式A->B|C的情况需满足：
>
> - FIRST(B)∩FIRST(C)=Ø；
>
>   > - B和C推导出的串不可有公共首字符；
>   > - B和C最多只有一个可产生空串；
>
> - 若ε∈FIRST(B)，则FOLLOW(A)∩FIRST(B)=Ø
>
>   > 若B->ε，B不可推导出以FOLLOW(A)中的终结符开头的串

## 练习4.2.2

考虑文法：
    lexp→atom | list
	atom→number | identifier
	list→(lexp-seq)
	lexp-seq→lexp-seq lexp | lexp

1. 消除左递归
2. 求得该文发的FIRST集合和FOLLOW集合
3. 说明所得的文法是LL(1)文法
4. 为所得的文法构造LL(1)分析表
5. 对输入串(a (b (2)) (c))给出相应得LL(1)分析程序的动作

---

1. ```
   lexp→atom | list
   atom→number | identifier
   list→(lexp-seq)
   lexp-seq→ lexp lexp-seq'
   lexp-seq'→ lexp lexp-seq'| ε
   ```

2. ```
   FIRST(list)={(}
   FIRST(atom)={number, identifier}
   FIRST(lexp)=FISRT(atom)+FIRST(list)={number, identifier}+{(}= {number, identifier, (}
   FIRST(lexp-seq')={ε}+FIRST(lexp)={ε, number, identifier, (}
   FIRST(lexp-seq)=FIRST(lexp)={number, identifier, (}
   ```
   
   ```
   FOLLOW(lexp)={$}+FIRST(lexp-seq')+FOLLOW(lexp-seq')={$, ε, number, identifier, (,)}
   FOLLOW(atom)=FOLLOW(lexp)={$, ε, number, identifier, (,)}
   FOLLOW(list)=FOLLOW(lexp)={$, ε, number, identifier, (,)}
   FOLLOW(lexp-seq)={)}
   FOLLOW(lexp-seq')=FOLLOW(lexp-seq)={)}
   ```
	
	> 求Follow和FIRST思路分析：自底向上，先求FIRST再求FOLLOW
	>
	> - 求FIRST：
	>
	>   - A->B|C则有`FIRST(A)=FIRST(B)∪FIRST(C)`
	>   - A->BC：
	>     - 若ε∈FIRST(B)，FIRST(A)=(FIRST(B)-ε)+FIRST(C)
	>     - 若ε∉FIRST(B)，FIRST(A)=FIRST(B)
	>
	> - 求FOLLOW：A->BC则有
	>
	>   - FOLLOW(A)=FOLLOW(C)
	>
	>     > Note：此处不考虑A还产生其他产生式的情况，即若有A->BC|D，还应有FOLLOW(A)=FOLLOW(C)∪FOLLOW(D)
	>
	>   - FOLLOW(B)=
	>
	>     - 若ε∈FIRST(C)，FOLLOW(B)=(FIRST(C)-ε)+FOLLOW(C)
	>     - 若ε∉FIRST(C)，FOLLOW(B)=FIRST(C)
	>
	>   - 若有A是起始符，将$加入FOLLOW(A)（相应地也要update FOLLOW(C)，这是唯一一个自顶向下的步骤）
	>
	>     > 个人习惯最后统一执行这一步，因为其他步骤都是自底向上的


3. 逐个检查产生式选择不唯一的非终结符：
   - `lexp→atom | list`:
     有`FIRST(list)∩FIRST(atom)=∅`、`list` 和`atom`皆不可导出空串；
   - `atom→number | identifier`: 
     number和identifier皆是终结符，有`FIRST(number)∩FIRST(identifier)=∅`、`number` 和`identifier`皆不可导出空串；
   - `lexp-seq'→ lexp lexp-seq'| ε`:
     `FIRST(lexp lexp-seq')∩FIRST(ε)=∅`、`FIRST(lexp lexp-seq')∩FIRST(lexp-seq')=∅`

​		故该文法为LL(1)

4. | 非终结符  | （                         | ）              | number                     | identifier                 | $    |
   | --------- | -------------------------- | --------------- | -------------------------- | -------------------------- | ---- |
   | lexp      | lexp → list                |                 | lexp → atom                | lexp → atom                |      |
   | atom      |                            |                 | atom→number                | atom→identifier            |      |
   | list      | list→(lexp-seq)            | list→(lexp-seq) |                            |                            |      |
   | lexp-seq  | lexp-seq → lexp lexp-seq'  |                 | lexp-seq → lexp lexp-seq'  | lexp-seq → lexp lexp-seq'  |      |
   | lexp-seq' | lexp-seq' → lexp lexp-seq' | lexp-seq'→ ε    | lexp-seq' → lexp lexp-seq' | lexp-seq' → lexp lexp-seq' |      |

>  构造预测分析表：对于文法G中全体`A->α`, 
>
> - a∈FTRST(α)  , `A->α`加入M [A,a]；
>
> - 若ε∈FIRST(α) 中 , 若b∈FOLLOW(A), 将`A->α`加入M[A, b] 中
>
>   > 对b是终结符也成立

4. 设lexp为S，atom为A，list为L，lexp-seq为Q，lexp-seq'为Q'，number为n，identifier为i：
   
   
   | 栈          | 输入             | 动作    |
   | ----------- | ---------------- | ------- |
   | \$S          | (a (b (2)) (c))\$ | S->L    |
   | \$L          | (a (b (2)) (c))\$ | L->(Q)  |
   | \$)Q(        | (a (b (2)) (c))\$ | match   |
   | \$)Q         | a (b (2)) (c))\$  | Q->SQ'  |
   | \$)Q'S       | a (b (2)) (c))\$  | S->A    |
   | \$)Q'A       | a (b (2)) (c))\$  | A->i    |
   | \$)Q'i       | a (b (2)) (c))\$  | match   |
   | \$)Q'        | (b (2)) (c))\$    | Q'->SQ' |
   | \$)Q'S       | (b (2)) (c))\$    | S->L    |
   | \$)Q'L       | (b (2)) (c))\$    | L->(Q)  |
   | \$)Q')Q(     | (b (2)) (c))\$    | match   |
   | \$)Q')Q      | b (2)) (c))\$     | Q->SQ'  |
   | \$)Q')Q'S    | b (2)) (c))\$     | S->A    |
   | \$)Q')Q'A    | b (2)) (c))\$     | A->i    |
   | \$)Q)Q'i     | b (2)) (c))\$     | match   |
   | \$)Q')Q'     | (2)) (c))\$       | Q'->SQ' |
   | \$)Q')Q'S    | (2)) (c))\$       | S->L    |
   | \$)Q')Q'L    | (2)) (c))\$       | L->(Q)  |
   | \$)Q')Q')Q(  | (2)) (c))\$       | match   |
   | \$)Q')Q')Q   | 2)) (c))\$        | Q->SQ'  |
   | \$)Q')Q')Q'L | 2)) (c))\$        | L->A    |
   | \$)Q')Q')Q'A | 2)) (c))\$        | A->n    |
   | \$)Q')Q')Q'n | 2)) (c))\$        | match   |
   | \$)Q')Q')Q'  | )) (c))\$         | Q'->ε   |
   | \$)Q')Q')    | )) (c))\$         | match   |
   | \$)Q')Q'     | ) (c))\$          | Q'->ε   |
   | \$)Q')       | ) (c))\$          | match   |
   | \$)Q'        | (c))\$            | Q'->SQ' |
   | \$)Q'S       | (c))\$            | S->L    |
   | \$)Q'L       | (c))\$            | L->(Q)  |
   | \$)Q')Q(     | (c))\$            | match   |
   | \$)Q')Q      | c))\$             | Q->SQ'  |
   | \$)Q')Q'S    | c))\$             | S->A    |
   | \$)Q')Q'A    | c))\$             | A->i    |
   | \$)Q')Q'i    | c))\$             | match   |
   | \$)Q')Q'     | ))\$              | Q'->ε   |
   | \$)Q')       | ))\$              | match   |
   | \$)Q'        | )\$               | Q'->ε   |
   | \$)          | )\$               | match   |
   | \$           | \$                | accept  |
