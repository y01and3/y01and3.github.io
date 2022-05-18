# Wiener Attack Note

# Wiener Attack 笔记
## 连分数
### 连分数形式
{{< raw >}}$$ \frac{a_1}{q_1+\frac{a_2}{q_2+\frac{a_3}{\ddots +\frac{a_{m-1}}{q_{m-1}+\frac{a_m}{q_m}}}}} $${{< /raw >}}

令 {{< raw >}}$a_i=1,\, i=1,2,\dots ,m${{< /raw >}} .

{{< raw >}}$$\langle q_0,q_1,\dots ,q_m \rangle = q_0+\frac{1}{q_1+\frac{1}{q_2+\frac{1}{\ddots +\frac{1}{q_{m-1}+\frac{1}{q_m}}}}}$${{< /raw >}}

### 将有理数转换为连分数

{{< raw >}}$$\begin{align*}
f&=\langle q_0,q_1,\dots ,q_m \rangle \\
q_0&=\lfloor f \rfloor &r_0&=f-q_0\\
q_i&=\lfloor \frac{1}{r_{i-1}} \rfloor &r_i&=\frac{1}{r_{i-1}} - q_i,\, i=1,2,\dots
\end{align*}$${{< /raw >}}

直到 {{< raw >}}$r_m=0${{< /raw >}} .

通过观察可以得到：

1. {{< raw >}}$$\begin{align*}
&q_m \geq 2\\
&若\; q_m=1,\, 那么\; r_{m-1}=1,\, 而\; 0< r<1\\
& \therefore q_m \geq 2
\end{align*}$${{< /raw >}}

2. {{< raw >}}$$\begin{align*}
&\forall x>0\\
&\begin{cases}
\langle q_0,q_1,\dots ,q_m \rangle<\langle q_0,q_1,\dots ,q_m+x \rangle ,\, m是偶数\\
\langle q_0,q_1,\dots ,q_m \rangle>\langle q_0,q_1,\dots ,q_m+x \rangle ,\, m是奇数
\end{cases}
\end{align*}$${{< /raw >}}

### 将连分数转换为有理数
{{< raw >}}$$\begin{align*}
\frac{n_i}{d_i} &= \langle q_0,q_1,\dots ,q_m \rangle , &GCD&(n_i,d_i)=1,\, &i&=0,1,\dots ,m\\
n_0&=q_0 &d_0&=1\\
n_1&=q_0\cdot q_1 &d_1&=q_1\\
n_i&=q_i\cdot n_{i-1}+n_{i-2} &d_i&=q_i \cdot d_{i-1}+d_{i-2} ,\, &i&=2,3,\dots ,m
\end{align*}$${{< /raw >}}

可以从中得到下面的关系：

{{< raw >}}$$ n_i\cdot d_{i-1}-n_{i-1}\cdot d_i=-(-1)^i,\, i=1,2,\dots ,m $${{< /raw >}}


## 连分数算法

{{< raw >}}$f^{\prime}=f(1-\delta),\, \delta \geq 0$，$f^{\prime}${{< /raw >}} 是 $f$ 的一个较小的估计值。

如果 $\delta$ 足够小，那么 $f$ 的分子和分母可以通过下面的方式找到：

1. {{< raw >}}$q_i^{\prime} =\lfloor \frac{1}{r_{i-1}}\rfloor${{< /raw >}}

2. {{< raw >}}$$\frac{n_i}{d_i}=
 \begin{cases}
 \langle q_0,q_1,\dots ,q_m+1 \rangle , &m是偶数\\
 \langle q_0,q_1,\dots ,q_m \rangle , &m是奇数
 \end{cases}$${{< /raw >}}
 
 3. 测试 $f=\frac{n_i}{d_i}$ 是否成立.
 
### 成功条件
{{< raw >}}$$\begin{align*}
\langle q_0,q_1,\dots ,q_m-1 \rangle < &f^{\prime}\leq \langle q_0,q_1,\dots ,q_m \rangle , m是偶数\\
\langle q_0,q_1,\dots ,q_m+1 \rangle < &f^{\prime}\leq \langle q_0,q_1,\dots ,q_m \rangle , m是奇数
\end{align*}$${{< /raw >}}

对于 $\delta$ 大小的影响：

$\delta =1-\frac{f^{\prime}}{f}$ .

根据 $m$ 的大小分为四种情况：
1. $m=0$ :

{{< raw >}}$$\begin{align*}
&\langle q_0-1\rangle < f^{\prime}\leq \langle q_0\rangle\\
&\delta < 1-\frac{\langle q_0-1\rangle}{\langle q_0\rangle}\\
\therefore& \delta <\frac{1}{q_0}\\
\therefore& \delta <\frac{1}{n_0\cdot d_0}\quad (n_0=q_0,\,d_0=1)
\end{align*}$${{< /raw >}}

2. $m=1$ :

{{< raw >}}$$\begin{align*}
&\langle q_0,q_1+1\rangle < f^{\prime}\leq \langle q_0,q_1\rangle\\
&\delta < 1-\frac{\langle q_0,q_1+1\rangle}{\langle q_0,q_1\rangle}\\
\therefore& \delta <\frac{1}{(q_0\cdot q_1+1)(q_1+1)}\\
\because& q_m\geq 2\\
\therefore& \frac 3 2 q_1\geq q_1+1\\
\therefore& \delta <\frac{1}{\frac 3 2 n_0\cdot d_0}\quad 是确保该算法成功的条件 
\end{align*}$${{< /raw >}}

3. $m$ 是偶数且 $m\geq2$ :

{{< raw >}}$$\begin{align*}
&\langle q_0,q_1,\dots ,q_m-1\rangle < f^{\prime}\leq \langle q_0,q_1,\dots ,q_m\rangle\\
&\delta < 1-\frac{\langle q_0,q_1,\dots ,q_m-1\rangle}{\langle q_0,q_1,\dots ,q_m\rangle}\\
\because& \langle q_0,q_1,\dots ,q_m\rangle=\frac{n_m}{d_m} =\frac{q_m n_{m-1}+n_{m-2}}{q_n d_{m-1}+d_{m-2}} \\
&\langle q_0,q_1,\dots ,q_m-1\rangle=\frac{(q_m-1)n_{m-1}+n_{m-2}}{(q_m-1)d_{m-1}+d_{m-2}} \\
\therefore& \delta < \frac{n_{m-1}d_{m-2}-n_{m-2}d_{m-1}}{(q_mn_{m-1}+n_{m-2})(q_md_{m-1}+d_{m-2}-d_{m-1})}\\
\because& n_{m-1}d_{m-2}-n_{m-2}d_{m-1}=-(-1)^{m-1}=1\\
\therefore& \delta<\frac{1}{n_m(d_m-d_{m-1})} \\
\therefore& \delta <\frac{1}{n_m\cdot d_m}\quad 是确保该算法成功的条件 
\end{align*}$${{< /raw >}}

4. $m$ 是奇数且 $m\geq3$ :

{{< raw >}}$$\begin{align*}
&\langle q_0,q_1,\dots ,q_m+1\rangle < f^{\prime}\leq \langle q_0,q_1,\dots ,q_m\rangle\\
&\delta < 1-\frac{\langle q_0,q_1,\dots ,q_m+1\rangle}{\langle q_0,q_1,\dots ,q_m\rangle}\\
\because& \langle q_0,q_1,\dots ,q_m\rangle=\frac{n_m}{d_m} =\frac{q_m n_{m-1}+n_{m-2}}{q_n d_{m-1}+d_{m-2}} \\
&\langle q_0,q_1,\dots ,q_m+1\rangle=\frac{(q_m+1)n_{m-1}+n_{m-2}}{(q_m+1)d_{m-1}+d_{m-2}} \\
\therefore& \delta < \frac{n_{m-1}d_{m-2}-n_{m-2}d_{m-1}}{(q_mn_{m-1}+n_{m-2})(q_md_{m-1}+d_{m-2}+d_{m-1})}\\
\because& n_{m-1}d_{m-2}-n_{m-2}d_{m-1}=-(-1)^{m-1}=1\\
\therefore& \delta<\frac{1}{n_m(d_m+d_{m-1})} \\
\because& d_m=q_md_{m-1}+d_{m-2},\, q_m\geq2\\
\therefore& d_m+d_{m-1}\leq\frac 3 2 d_m\\
\therefore& \delta <\frac{1}{\frac 3 2n_m\cdot d_m}\quad 是确保该算法成功的条件 
\end{align*}$${{< /raw >}}


## 连分数算法在 RSA 的使用

{{< raw >}}$$\begin{align*}
&ed \equiv 1 \pmod {LCM(p-1,q-1)}\\
\therefore &ed \equiv K\cdot LCM(p-1,q-1)+1,\, K\in Z\\
\end{align*}$${{< /raw >}}

令 $G=GCD(p-1,q-1)$ .

$$ ed=\frac{K}{G}\cdot (p-1)(q-1)+1 $$

令 {{< raw >}}$k=\frac{K}{GCD(p-1,q-1)},\, g=\frac{G}{GCD(K,G)}${{< /raw >}} .

{{< raw >}}$$\begin{align*}
&ed=\frac{k}{g}(p-1)(q-1)+1\\
\therefore &\frac{e}{N}=\frac{k}{dg}(1-\delta),\, \delta=\frac{p+q-1-\frac{g}{k}}{N}\\
\because &GCD(K,d)=1\\
\therefore &GCD(k,d)=1\\
\because &GCD(k,g)=1\\
\therefore &GCD(k,dg)=1\\
\because &f=\frac{n_m}{d_m}=\frac{k}{dg}\\
\therefore &\delta=\frac{p+q-1-\frac{g}{k}}{N}<\frac{1}{\frac 3 2 kdg}\\
\therefore &kdg<\frac{N}{\frac 3 2(p+q)}
\end{align*}$${{< /raw >}}

测试猜想的 $k$, $dg$ 是否正确：

假设 $ed>N$ ，那么 $k>g$ .

{{< raw >}}$$\begin{align*}
&edg=k(p-1)(q-1)+g\\
\because &k>g\\
\therefore &(p-1)(q-1)=\lfloor \frac{edg}{k}\rfloor ,\, g \equiv edg \pmod k
\end{align*}$${{< /raw >}}

$\frac{N-(p-1)(q-1)+1}{2}=\frac{p+q}{2}$ ，如果 $(p-1)(q-1)$ 为 0 或是奇数，那么猜想错误。

$\big(\frac{p+q}{2}\big)^2-N=\big(\frac{p-q}{2}\big)^2$ ，如果 $\big(\frac{p-q}{2}\big)^2$ 是完全平方数，那么猜想正确。

{{< raw >}}$$\begin{cases}
p=\frac{p+q}{2}+\frac{p-q}{2}\\
q=\frac{p+q}{2}-\frac{p-q}{2}
\end{cases}$${{< /raw >}}

在没有防止连分数攻击的情况下，可以期望 $g$ 比较小， $k<dg$ 。在这种情况下 $d$ 的位数约为 $N$ 的 $\frac 1 4$ ，在多项式时间内可以找到这样的 $d$ 。


## 流程

1. 计算 {{< raw >}}$q_i^{\prime},\, r_i^{\prime},\, \frac{n_i^{\prime}}{d_i^{\prime}}${{< /raw >}}
2. 猜测 $\frac{k}{dg}$ 
3. 猜测 $edg = e\cdot dg$
4. 猜测 $(p-1)(q-1)=\lfloor \frac{edg}{k} \rfloor$
5. 猜测 $g\equiv edg\pmod k$
6. 猜测 $\frac{p+q}{2}$ 
7. 猜测 $\big(\frac{p-q}{2}\big)^2$ 
8. 计算 $d$

一轮中止之后，开始新的一轮，直到得到 $d$ 。

## 防止连分数攻击

1. 增大 $k$ 。必须增大 $e$ ，可以通过给 $e$ 增加 $LCM(p-1,q-1)$ 的倍数实现。 $e>(pq)^{\frac{3}{2}}$ 时，无法进行连分数攻击。

2. 增大 $g$ 。增大 $GCD(p-1,q-1)$ ，但是在特定条件下可以找到 $g$ 或 $g$ 的因数。


## 改进
1. 允许寻找 $d$ 的时候略微超过 $kdg<\frac{N}{\frac 3 2(p+q)}$ .

2. 使用对 $(p-1)(q-1)$ 更加接近的估计替换 $N$ :

可以使用 $\lfloor (\sqrt N-1)^2\rfloor$ ，那么 $kdg<\frac 2 3\big(\frac{\sqrt N-1}{\sqrt p-\sqrt q}\big)^2$ .

3. 在多个 $\frac{k}{dg}$ 的猜想上使用算法。（？）

4. 试图寻找 $g$ 或 $g$ 的因数：

假设 $t$ 是 $g$ 的一个未知的因数。

$t\big(\frac{e}{N} \big)$ 是 $\frac{k}{d(\frac{g}{t})}$ 的一个较小的估计。

{{< raw >}}$$\begin{align*}
&kd\big(\frac{g}{t}\big)<\frac{N}{\frac 3 2 (p+q)}\\
\because &N-1=(p-1)(q-1)+(p-1)+(q-1)\\
&g|(p-1)\qquad g|(q-1)\\
\therefore &g|(N-1)
\end{align*}$${{< /raw >}}

如果 $g$ 或 $g$ 的所有质因数很大，那么将难以计算 $g$ 的因数。然而， $g$ 过大将导致 $\frac{p-1}{g}$ 和 $\frac{q-1}{g}$ 很小，可以通过查找 $\frac{p-1}{g}$ 和 $\frac{q-1}{g}$ 来寻找 $g$ 的可能值。

## 一些废话

这篇论文还比较容易看懂，至少对于我来说比网上写的总结好懂一些。

因为翻译水平比较一般，所以可能会有一股机翻味（？）。

