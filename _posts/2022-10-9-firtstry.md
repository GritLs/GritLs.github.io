---
layout: article
title: 扩展欧几里德算法
---


<font face="宋体" size=2 color="black" >  

## 扩展欧几里得算法
####  问题描述
给定$n$对正整数$a_i,b_i$,对于每一对数，求出一组$x_i,y_i$,使其满足
$a_ix_i+b_iy_i=gcd(a,b)$  

#### 0.裴蜀定理
对于整数$a,b$ 有 $d=gcd(a,b)$则对于任何的整数$x,y$则一定有$d|ax+by$，即$ax+by$一定是$gcd(a,b)$的倍数，特别的，一定存在整数$x,y$使得$ax+by=gcd(a,b)$成立。  
如果$ax+by=t$,而$t$不能被$d$整除，则该方程无整数解。
#### 1.扩展欧几里得
用于求解方程$ax+by=gcd(a,b)$的解

当$b\neq0$时，因为$gcd(a,b)=gcd(b,a\%b)$则有  

$$
\\bx'+(a\%b)y'=gcd(b,a\%b)\tag{1}  
$$
易得   $a\%b=a-\lfloor a/b \rfloor*b$,将其带入$(1)$式中可得  
$$
bx'+(a-\lfloor \frac{a}{b} \rfloor*b)y'=gcd(b,a\%b)
\\b(x'-\lfloor \frac{a}{b} \rfloor)+ay'=gcd(b,a\%b)
$$
由此可得

$$
\left\{ 
\begin{array}{c}\tag{2}
    x=y' \\ 
    y=x'-\lfloor \frac{a}{b} \rfloor *y'\\ 
\end{array}
\right. 
$$
观察原有的欧几里得算法
```cpp
int gcd(int a,int b)
{
    if(!b){
        return a;
    }
    return gcd(b,a%b);
}
```
我们所要求的$x,y$在每一步的辗转相除中都会改变，但在最后一步，递归的出口一定是$x=1,y=0$。因为在辗转相除法的最后一步，$b$一定等于$0$且返回值$a$即为所求的最大公约数，即
$$
a*x+0*y=gcd(a,0)=a
$$
因此必有特解$x=1,y=0$ 这里注意一下$x$是只能取$1$,但是$y$可以取任何值，为了计算方便我们将其取做$0$。
由刚刚推导的$(2)$我们可以知道,每一步的$x$和$y$都与下一步的$x',y'$以上述公式的方式相联系，因此我们可以修改一下$gcd$函数便可以得到一段很漂亮简洁的代码。
```cpp
int exgcd(int a,int b,int &x,int &y)
{
    if(!b)
    {
        x=1,y=0;
        return a;
    }
    int d,x1,y1; //我们需要用到从上一层递归栈中传过来的d,x1,y1因此这里的x和y都要传地址
    d=exgcd(b,a%b,x1,y1);
    x = y1;
    y = x1 - (a/b)*y1; 
    return d;
}
```

#### 2.对于求解更一般的方程$ax+by=c$
**设$d=gcd(a,b)$,该式成立当且仅当$d | c$**
求解方法如下:  
首先用扩展欧几里得算法求出$ax+by=d$的特解  
设特解为$x=x_0,y=y_0$ ,即$ax_0+by_0=d$
于是$ax+by=c$的特解为
$$
ax_0(c/d)+by_0(c/d) = c
$$
**通解=特解+齐次解**
$ax+by=0$的解为$x=b/d,y=-a/d$
因此一般形式的解为
$$
x=x_0(c/d)+k*(b/d)
\\y=y_0(c/d)-k*(a/d)
$$
这里注意一下为什么齐次解是$x=b/d,y=-a/d$而不是$x=b,y=-a$,因为显然前者在乘上$k$后可以取到更多的值。  
#### 3.求解一次同余方程$ax\equiv b(mod\ m)$
等价于求$ax=m*(-y)+b$ 即 $ax+my=b$
有解的条件为 $gcd(a,m)|b$ 
特别的，当$b=1$且$a,m$互质时所求的$x$即为$a$的逆元
因为$ax+my=gcd(a,m)$ 而 $b=1$ 故 $gcd(a,m)=1$ 即 $a,m$ 互质。