# 特赦囚犯问题策略分析

<p align="right">2020.08.13 Thur.</p>

## 问题背景

当对一百名囚犯进行特赦的时候，典狱长采取了如下的方法决定是否对他们特赦。典狱长为这一百名囚犯在一个密闭的房间内提供了一百个从1至100编号的抽屉并不重复地随机装入从1到100编号的号码牌，对应的也为每一个囚犯从1到100进行了编号，这些囚犯将会依次进入这个密闭的房间，任意开启50个抽屉，如果这50个抽屉中存在该囚犯对应的号码牌，则认为该囚犯查询成功，如果这50个抽屉中不存在该囚犯对应的号码，则认为该囚犯查询失败，当该囚犯离开房间时，抽屉将会恢复初始状态，在第一个囚犯进入密闭房间后囚犯间就不允许交流了，如果存在囚犯查询失败，则会取消特赦。



## 初步分析

如果所有的囚犯独立地随机地挑选抽屉寻找自己的号码，则可以得出每一个囚犯能够成功相当于从两个集合中选取一个包含自己号码的集合，其概率为$\frac{1}{2}$，而对于所有100个囚犯而言，全部成功的概率为$\frac{1}{2^{100}}$约为$7.888609 \times 10^{-31}$ ，在这种情况下特赦取消的概率为$1-\frac{1}{2^{100}}$约等于$1$，因此在当所有囚犯均完全随机挑选打开抽屉的情况下，囚犯被特赦的几率非常小。

为此有人提出了一个策略，每一个囚犯首先开启自己对应号码的抽屉，当获得的号码与囚犯对应的编号不同时依次打开抽屉内号码牌对应号码的抽屉。

对于这样一个策略，可以将这一百个抽屉和对应的号码牌视作一百个散点，将每一组抽屉和号码牌的对应关系视作一条有向边，抽屉号码与号码牌对应相同的情况对应了一个自环，规定这个有向图的每一个点只允许存在一个出度和一个入度，且必然包含于某一个环内。

在这样的策略下，首先假设每一个囚犯会依次打开抽屉直到获得与该囚犯对应相同的号码为止，那么在这样的情况下，每打开一个相当于向有向图内新增一条边，直到组成一个环为止。对于每一个囚犯而言，这个过程是相同的。所有囚犯能否被特赦就取决于那个密闭空间的抽屉对应的图中最大环的长度是否没有超过50。



## 推导计算

经过上述分析可知，所有囚徒可以被特赦的概率等同于一个100个出度入度均为1的点组成的有向图中最大环长度不超过50的概率，以下简称为最大环长度不超过50的概率。

由于计算最大环长度不超过50的概率相对复杂，此处选择对应的补事件，计算最大环长度超过50的概率。由于图是有向图，且每一个点的出度入度均为1，图内所有的点都需要包含于且仅包含于某一个环内，且在该图内，当存在一个最大环的长度大于50时，未被包含于该环的点一定小于50个，不可能存在另一个不同的环长度大于50，因此该图内最大环长度为51与其他情况均为互斥事件，最大环长度为52, 53 ... 100的情况同理。因此对于最大环长度大于50的概率，可以通过如下方式计算概率。

设事件A为最大环长度不超过50，设事件B为最大环长度超过50，X表示最大环长度。

$$P(A)=1-P(B)$$

$$P(B)=P(X=51)+P(X=52)+\cdots+P(X=100)$$

对于存在最大环长度为51的情况而言，其概率可以通过如下方式计算。

对于长度为51的环而言，随机选取51个元素，他们组成一个环的所有可能为$C_{100}^{51}\times50!$种。

对于剩余的49个点而言，他们组成环的所有可能共有$49!$种。

对于100个点而言，组成环的所有可能共有$100!$种。

$$P(X=51)=\frac{C_{100}^{51}\times50!\times49!}{100!}=\frac{\frac{100!}{51!\times49!}\times50!\times49!}{100!}=\frac{1}{51}$$

同理可以推导假设$x（50\lt x \leqslant100）$为最大环长度时：

对于最大环而言，随机选取$x$个点，他们组成一个环的所有可能为$C_{100}^{x}\cdot(x-1)!$种。

对于剩余的$(100-x)$个点而言，他们组成环的所有可能共有$(100-x)!$种。

$$P(X=x)=\frac{C_{100}^{x}\cdot(x-1)!\cdot(100-x)!}{100!}=\frac{\frac{100!}{(x-1)!\cdot(100-x)!}\cdot x!\cdot(100-x)!}{100!}=\frac{1}{x}$$

由$P(B)=P(X=51)+P(X=52)+\cdots+P(X=100)$可以得到：

$P(B)=\frac{1}{51}+\frac{1}{52}+\cdots+\frac{1}{100}=\underset{n=51}{\overset{100}{\Sigma}}\frac{1}{n}\approx0.68817217931019520326$

由$P(A)=1-P(B)$可以得到：

$P(A)=1-P(B)=1-\underset{n=51}{\overset{100}{\Sigma}}\frac{1}{n}\approx0.31182782068980479674$

因此采用上述的策略可以有约31%的概率能够使得所有囚犯获得特赦。



## 模拟结果

采用Java程序模拟完全随机100次和上述策略100次，结果如下。



## 推广分析

### 推广至n趋于无穷的情况

以上的分析思路可以应用于抽屉的总数为$2n$，允许开启$n$个抽屉，或者抽屉的总数为$2n+1$，允许开启$n+1$个抽屉的情况。$(n\gt0)$

首先对于总数为$2n$的情况，可以推导假设$x（n\lt x \leqslant2n）$为最大环长度时：

设事件A为最大环长度不超过$n$，设事件B为最大环长度超过$n$，X表示最大环长度。

对于最大环而言，随机选取$x$个点，他们组成一个环的所有可能为$C_{2n}^{x}\cdot(x-1)!$种。

对于剩余的$(2n-x)$个点而言，他们组成环的所有可能共有$(2n-x)!$种。

$$P(X=x)=\frac{C_{2n}^{x}\cdot(x-1)!\cdot(2n-x)!}{(2n)!}=\frac{\frac{(2n)!}{(x-1)!\cdot(2n-x)!}\cdot x!\cdot(2n-x)!}{(2n)!}=\frac{1}{x}$$

由$P(B)=P(X=n+1)+P(X=n+2)+\cdots+P(X=2n)$可以得到：

$P(B)=\frac{1}{n+1}+\frac{1}{n+2}+\cdots+\frac{1}{2n}=\underset{\alpha=n+1}{\overset{2n}{\Sigma}}\frac{1}{\alpha}$

根据调和级数公式：$\underset{\alpha=1}{\overset{n}{\Sigma}}\frac{1}{\alpha}=ln(n)+\gamma$，其中$\gamma$为欧拉常数。

$\gamma=\underset{n\rightarrow \infty}{\lim}\int_{1}^{n}(\frac{1}{\lfloor x \rfloor}-\frac{1}{x})dx\approx0.577215664$

我们可以利用$\underset{\alpha=1}{\overset{2n}{\Sigma}}\frac{1}{\alpha}-\underset{\alpha=1}{\overset{n}{\Sigma}}\frac{1}{\alpha}$得到$\underset{\alpha=n+1}{\overset{2n}{\Sigma}}\frac{1}{\alpha}$

$P(B)=\underset{\alpha=n+1}{\overset{2n}{\Sigma}}\frac{1}{\alpha}=ln(2n)+\gamma-(ln(n)+\gamma)=ln(2n)-ln(n)=ln(2)$

由$P(A)=1-P(B)$可以得到：

$P(A)=1-P(B)=1-\underset{\alpha=n+1}{\overset{2n}{\Sigma}}\frac{1}{\alpha}=1-ln(2)\approx0.306853$

因此采用上述的策略当n趋于无穷时可以有约30%的概率能够使得所有囚犯获得特赦。



### 探究分组对概率的影响

100个囚犯进行一次活动和100个囚犯分为两组分别活动的对比。

两组全部完成特赦，两组结果一致特赦，两组分别特赦。



## 代码验证

概率计算

```Java
import java.util.*;
import java.lang.*;
import java.math.BigDecimal;
import java.math.RoundingMode;

public class Main {
	public static void main(String []args) {
		BigDecimal bigDecimal = new BigDecimal(0);
		BigDecimal one = new BigDecimal(1);
		try {
			for(int i=51; i<101; i++) {
				bigDecimal = bigDecimal.add(one.divide(new BigDecimal(i),20,RoundingMode.HALF_DOWN));
			}
		} catch(Exception ex) {
			ex.printStackTrace();
			System.out.println("Error Occured.");
		}
		finally {
			System.out.println(bigDecimal);
			System.out.println(one.subtract(bigDecimal));
		}
	}
}

//Output:
//0.68817217931019520326
//0.31182782068980479674

```



模拟实验 100次完全随机

```Java
import java.util.*;
import java.lang.*;
```
