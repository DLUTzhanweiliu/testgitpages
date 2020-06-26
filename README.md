<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<center>梯级库群短期负荷分配方法—LINGO</center>
# 了解篇
短期水电调度分为两种模式
（1）“以电定水”模式
对于计划期T，在已知水电站总负荷过程和预测入库流量过程等条件下，寻求所采用的优化准则达到极值的水电站各时段工作机组的最优台数、组合即有功负荷在工作机组间的最有分配、以及相应的水库蓄放水状态变化过程和泄流设施控制过程。
常用的优化准则包括：计划期内T的水电站输入能Ein(T)最小、效率$\eta\left(T\right)$最大、能量损失∆E(T)最小、水电站引用水量W（T）最小等。$$\eta$$
（2）“以水定电”模式
对于计算期T，在已知预测入库流量过程和总用水量等条件下，寻求使所采用的优化准则
达到极值的水电站总负荷过程、各时段工作机组的最优台数、组合、有功负荷在工作机组间的最优分配，以及相应的水库蓄泄状态变化过程和泄流设施控制过程。
常用准则：计算期T内的水电站总发电量E（T）最大或总发电效益Ec（T）最大。
<div>$123$</div>
<span>123\gamma</span>
\begin{equation}
\gamma
\end{equation}

本篇基于LINGO求解器，进行优化问题求解，所以提前了解LINGO的使用和问题的调试是基础，以下给出参考的资料，也可自己百度或官网学习。
https://wenku.baidu.com/view/af9b2c5d03d276a20029bd64783e0912a3167cd5.html
此为LINGO的基础使用方法，可便于新手了解和快速上手应用。仅供参考
 [测试文档](https://dlutzhanweiliu.github.io/testgitpages/testfile)
此为官网使用手册可更进一步详细学习和查看。

案例
具体操作步骤如下：
采用“以电定水”为例，以短期日调度过程，一天24小时以间隔15min为一时段进行划分，共计96点。案例采用天生桥一级，天生桥二级两个水电站梯级调度优化过程，利用商业软件LINGO求解器，具体操作如下：
一、优化准则：
目标函数：
满足所有水电站运行的约束要求情况下，水电站总的发电用水量W（T）最小准则，相应的目标函数为：
W_t=\min\funcapply{\sum_{t=1}^{T}\sum_{r=1}^{n}{[Q_{r,t}^f}×∆t×3600}，t=1,2,…,97; r=1,2;
式中：r表示水电站的编号；t为时段编号，∆t表示t时段以小时为单位的时段长∆t =0.25h，W_t为计划期内发电用水量，m3；Q_{r,t}^f表示第r号水电站在t时段的发电流量；
约束条件
	功率平衡：
\sum_{r=1}^{n}P_{r,t}=P_t
式中：P_{r,t}为t时段第r号水电站承担的有功负荷，KW；P_t为t时段需求的有功负荷值，KW； 
	库水位，库蓄水量：
Z_{r,t}^{min}\le Z_{r,t}\le Z_{r,t}^{max},\forall r,\forall t
V_{r,t}^{min}\le V_{r,t}\le V_{r,t}^{max},\forall r,\forall t
式中：Z_{r,t}^{min}，Z_{r,t}^{max}分别表示t时刻末水库上游允许最低、最高水位，m；V_{r,t}^{min}，V_{r,t}^{max}分别为t时刻末水库上游允许最小、最大蓄水量，m3; 
	水位-库容曲线：
Z_{r,t}=f_{ZV}\left[V_{r,t}\right],\forall r,\forall t
式中：Z_{r,t}表示t时段末水库上游水位，m；f_{ZV}（*）表示库容曲线函数。
	水库水量平衡：
V_{r,t+1}=V_{r,t}+\left[Q_{r,t}^r-Q_{r,t}^c\right]\times∆t×3600
Q_{r,t}^c=Q_{r,t}^f+Q_{r,t}^q,\forall t,\forall r
式中：V_{r,t}，V_{r,t+1}分别表示t时刻r号水电站初、末库蓄水量，m3;\ Q_{r,t}^r,\ Q_{r,t}^c,\ Q_{r,t}^q分别表示t时段r号水电站平均入库流量、出库流量和弃水流量，m3/s；
	尾水位-泄流量曲线关系约束：
Z_{r,t}^{down}=f_{ZQ}\left[Q_{r,t}^c\right],\forall r,\forall t
式中：Z_{r,t}^{down}为t时刻下游平均水位，m；f_{ZQ}（*）表示下游水位流量关系函数。
	出库流量约束：
Q_{r,t}^{c,min}\le Q_{r,t}^c\le Q_{r,t}^{c,max},\forall r,\forall t
   式中：Q_{r,t}^{min}，Q_{r,t}^{max}分别为t时段末水库的允许最小、最大出库流量，m3/s；
	净水头：
H_{r,t}=\frac{Z_{r,t}+Z_{r,t+1}}{2}-Z_{r,t+1}^{down}-∆Hr,t,∀r,∀t
∆Hr,t=f∆H,r[Qr,t] ,∀r,∀t
式中：H_{r,t}，∆Hr,t分别表示t时段r号水电站的水头和水头损失，m；f∆H,r为第r号水电站的水头损失函数。
	NQH（机组动力特性）曲线：\bigmP_{r,t}^{min}\le P_{r,t}\le P_{r,t}^{max},\forall r,\forall t
P_{r,t}=f_{PQH}[Q_{r,t},H_{r,t}]
式中：P_{r,t}^{min},\ P_{r,t}^{max}分别为t时段第r号水电站的允许最小、最大出力，KW；f_{PQH}表示出力计算函数。
（9）水电站出力爬坡：限制电站在相邻时段间的出力变幅，一般由机组的调节速率和电网安全需求确定：
|P_{r,t}-P_{r,t-1}|\le∆Pr
式中∆Pr为r电站单时段最大出力升降限制，KW；
（10）最小开机容量约束：保证电站开机满足最小运行出力，且同样适用于电站停机情况
P_{r,t}(P_{r,t}-P_{r,min})\geq0
   式中P_{r,min}为r电站最小开机出力，KW
（11）水电站限制运行区：反映电站在某些水头或出力下的气蚀和振动区域，应尽量避免运行在这些范围内，保证电网安全生产：
(P_{r,t}-{\underline{P^o}}_{r,t})(P_{r,t}-{\bar{P^o}}_{r,t})>0,\forall i,\forall r,\forall o
式中{\bar{P^o}}_{r,t}，{\underline{P^o}}_{r,t}分别为r电站机组限制运行区上下限，KW。
（12）机组最小开停机持续时间及次数：避免机组开停机的频繁启停，损耗机组寿命
   
(13)最小开机出力
(P_{m,t}-{Pmin}_m)P_{m,t}\geq0
(14)电站出力波动限制
      
(15)泄流最大过水能力
S_{m,t}\le\bar{S_{m,t}}
(16)水电系统带宽
      
二、初始数据查询：
电站基础信息
 
以上数据大部分来自南方电网数据库查询，其中水位-库容系数，尾水位-泄量系数需通过直线拟合成不同次线性函数（例：此次计算拟合的水位库容方程为：V=a*Z^4+b*Z^3+c*Z^2+d*Z+e）可通过编程或excel等方式进行拟合求解系数。
选用汛期2018年7月1日的当日入库流量信息和出力过程。数据如下：
 
三、LINGO17.0编程求解如下：

 

You can use the [editor on GitHub](https://github.com/DLUTzhanweiliu/testgitpages/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# 你好
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/DLUTzhanweiliu/testgitpages/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
