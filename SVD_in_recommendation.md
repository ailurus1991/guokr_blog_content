Title: SVD in recommendation
Date: 2010-12-03 10:20
Category: Review

# 产品形态

![svd1.png](resources/7389BB7011A3033CF25F8170A7211120.png)

![svd2.png](resources/D2614EF2DCE3EC5DF420DB6C65E174A1.png)
# 从奇异值分解在图像领域的应用说起
原图尺寸：500 x 469
那么这样的一张图片对应的矩阵大小即为：500 x 469，我们将这个矩阵不妨计做 A。
![dayi_original.png](resources/D02D1821F2AFBB5C647145646698062A.png)

A 的奇异值分解为：

$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+...+\sigma_ru_rv_r^T$$


直观地说，就是将矩阵A拆分成了若干个$\textbf{秩一矩阵}$其中等式右边每一项前的系数$\sigma$就是奇异值，$u$和$v$分别表示列向量，$\textbf{秩一矩阵}$的意思是矩阵的秩为1。我们假定奇异值满足
$$\sigma_1\geq\sigma_2\geq...\geq\sigma_r>0$$

既然奇异值有大小之分，那么它们对于对于矩阵来区别体现在哪里呢？

如果我们只保留等式右边的第一项，这个矩阵，或者说这个图片会变成什么样呢？

---
只保留第一项：
$$A=\sigma_1u_1v_1^T$$
![dayi_k1.png](resources/7C49288D9E62D48C740ED776E1CD5D86.png)

保留两项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T$$
![dayi_k2.png](resources/A3EF4C59A3CED4D3E471B3412589F199.png)

保留三项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+\sigma_3u_2v_3^T$$
![dayi_k3.png](resources/43719521A4EF96DE244AF90A990261A2.png)

保留五项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+\sigma_3u_3v_3^T+..+\sigma_5u_5v_5^T$$
![dayi_k5.png](resources/B3A236BD63D347163C91F0DEB0CE85FD.png)

保留十项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+\sigma_3u_3v_3^T+..+\sigma_{10}u_{10}v_{10}^T$$
![dayi_k10.png](resources/56E7776ECC441D97A28C5CB1A25606A9.png)

保留二十项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+\sigma_3u_3v_3^T+..+\sigma_{20}u_{20}v_{20}^T$$
![dayi_k20.png](resources/6A59D6D6B5E6F57F64B0D149C6E79E5C.png)

保留五十项：
$$A=\sigma_1u_1v_1^T+\sigma_2u_2v_2^T+\sigma_3u_3v_3^T+..+\sigma_{50}u_{50}v_{50}^T$$
![dayi_k50.png](resources/9EBBA95CF7928F88219ACE8635EB5962.png)

到这里，我们发现使用等式右边前50项就已经可以获得一张看起来与原图差别不大的图像。

我们不妨把 $A$ 的奇异值分解后的等式取前50项的结果记作 $A_{50}$。我们此时可以认为：

$$A \approx A_{50}$$

那么这样做的好处在哪儿呢？

原图我们需要存储大约：500 x 469 = 234,500 个数据，而如果使用前50项的话，只需要存储：50 x (1 + 500 + 469) = 48,500 个数据。存储量大约只有原来的五分之一。

那么现在我们可以明确：
1. 奇异值往往对应着矩阵中隐含的重要信息，且重要性和奇异值大小正相关。
2. 每个矩阵A都可以表示为一系列秩为1的“小矩阵”之和，而奇异值则衡量了这些“小矩阵”对于A的权重。

接下来我们需要搞清楚：
究竟奇异值代表了矩阵的什么信息？

# 从线性代数的几何意义开始说起

首先要明确：矩阵 == 线性变换

对于对角矩阵 M:
$$
 \left[
 \begin{matrix}
   3 & 0 \\
   0 & 1 \\
  \end{matrix}
  \right]
$$
我们将它作用于任何一向量上：
$$
 \left[
 \begin{matrix}
   3 & 0 \\
   0 & 1 \\
  \end{matrix}
  \right]
  
  \left[
 \begin{matrix}
   x \\
   y \\
  \end{matrix}
  \right]
  =
   \left[
 \begin{matrix}
   3x \\
   y \\
  \end{matrix}
  \right]
$$

那么在几何上，我们可以这样理解向量 M，它做的实际上是将一组向量纵向不变，横向拉长为原来的三倍：
![xx1.jpg](resources/D2B6E1C4CF3E0B82440E50D4B1E721BB.gif)

![xx2.jpg](resources/F7070F64EAD0ED61257E4D3456490D00.gif)

那倘若M 为：
$$
 \left[
 \begin{matrix}
   2 & 1 \\
   1 & 2 \\
  \end{matrix}
  \right]
$$
我们同样可以找到一组网格的方向，使得在网格方向上只存在拉伸作用。
![xx3.jpg](resources/EF930AF88B142AE2D461D16CA83AA454.gif)
![xx4.jpg](resources/C4AF6C9666ECC3083479DC375D21D43E.gif)

考虑更一般的非对称矩阵
\begin{bmatrix}
1 & 1 \\
0 & 1
\end{bmatrix}
很遗憾，此时我们再也找不到一组网格，使得矩阵作用在该网格上之后只有拉伸变换。

我们退求其次，找一组网格，使得矩阵作用在该网格上之后允许有$\textbf{拉伸变换和旋转变换}$，但要保证变换后的$\textbf{网格依旧互相垂直}$。

![xx5.jpg](resources/67E4714024CE336347226C9267071F31.gif)
![xx6.jpg](resources/577D6CF3F4C75BDDBA1047CA0801E766.gif)

那么下面我们开始正式介绍奇异值分解的几何意义。

奇异值分解的几何含义为：对于任何的一个矩阵，我们要找到一组两两正交单位向量序列，使得矩阵作用在此向量序列上后得到新的向量序列保持两两正交。

以及，奇异值的几何含义为：这组变换后的新的向量序列的长度。

![xx7.jpg](resources/4EE1577BDB3307F27FD50D488BD60C66.gif)
![xx8.jpg](resources/DF9A6EBB6506BFD6022D74D96E0FA16F.gif)

当矩阵M作用在正交单位向量$v_1$和$v_2$上之后，得到$Mv_1$和$Mv_2$也是正交的。令$u_1$和$u_2$分别是$Mv_1$和$Mv_2$方向上的单位向量，即:

$$Mv_1=\sigma_1 u_1，Mv_2=\sigma_2 u_2$$

写在一起就是:
$$M\left[ v_1\quad  v_2 \right]=\left[ \sigma_1u_1\quad  \sigma_2u_2 \right]$$

整理得：

$$M=M\left[ v_1\quad  v_2 \right]
\begin{bmatrix}
v_1^{\rm T} \\
v_2^{\rm T}
\end{bmatrix}
=\left[ \sigma_1u_1\quad  \sigma_2u_2 \right]
\begin{bmatrix}
v_1^{\rm T} \\
v_2^{\rm T}
\end{bmatrix}
=\left[ u_1\quad  u_2 \right]
\begin{bmatrix}
\sigma_1 & 0 \\
0 & \sigma_2
\end{bmatrix}
\begin{bmatrix}
v_1^{\rm T} \\
v_2^{\rm T}
\end{bmatrix}
$$

于是，这就是矩阵M的奇异值分解。奇异值$\sigma_1$和$\sigma_2$分别是$Mv_1$和$Mv_2$的长度。很容易可以把结论推广到一般n维情形。

下面给出一个更简洁更直观的奇异值的几何意义。

假设矩阵的奇异值分解为
$$
A=\left[ u_1\quad u_2 \right]
\begin{bmatrix}
3 & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
v_1^{\rm T} \\
v_2^{\rm T}
\end{bmatrix}
$$

其中$u_1,~u_2,~v_1,~v_2$是二维平面的向量。根据奇异值分解的性质，$u_1,~u_2$线性无关，$v_1,~v_2$线性无关。那么对二维平面上任意的向量x，都可以表示为：$x=\xi_1 v_1+\xi_2 v_2$ 。

当A作用在x上时，
$$
y=Ax=A[v_1\quad v_2]
\begin{bmatrix}
\xi_1 \\
\xi_2
\end{bmatrix}=
\left[ u_1\quad u_2 \right]
\begin{bmatrix}
3 & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
v_1^{\rm T} \\
v_2^{\rm T}
\end{bmatrix}
[v_1\quad v_2]
\begin{bmatrix}
\xi_1 \\
\xi_2
\end{bmatrix}=3\xi_1u_1+\xi_2u_2
$$


令 $\eta_1=3\xi_1,~\eta_2=\xi_2$，我们可以得出结论：如果x是在单位圆 $\xi_1^2+\xi_2^2=1$ 上，那么y正好在椭圆 $\eta_1^2/3^2+\eta_2^2/1^2=1$ 上。这表明：矩阵A将二维平面中单位圆变换成椭圆，而两个奇异值正好是椭圆的两个半轴长。

![xx9.jpg](resources/923428175BC7944CAC75C5517C07587A.jpg)

那么让我们继续推广至高维空间的一般情形：
一般矩阵A将单位球
$$\|x\|_2=1$$
变换为超椭球面
$$E_m=\{y\in {\bf C}^m:~y=Ax,~x\in{\bf C}^n,~\|x\|_2=1\}$$
那么矩阵A的每个奇异值恰好就是超椭球的每条半轴长度。

空间假想：
我们将空间超椭球的长轴优先保留下来，短轴我们渐渐忽略，得到的最终几何体其实与真实的不规则的椭球形状是非常接近的。

成功从几何上解释了为什么保留了较大的奇异值信息就能保留原始矩阵的重要信息。

# 试着总结一下？
这么说来好像奇异值分解其实是对矩阵的一种降维手段？

如果只关注奇异值，好像是这么回事儿。

但是有个问题，奇异值分解中我们只关注了奇异值，它代表了与之相乘的秩一矩阵的权值，那么这些秩一矩阵代表了什么？！

![xx9.jpg](resources/923428175BC7944CAC75C5517C07587A.jpg)

没错，回到几何上，秩一矩阵代表的其实是轴的方向。

# 说说 LSI（浅层语义分析）
title 含有的词表：
![lsi1.png](resources/6A9846FCBF2615947D4634BE30CB9C76.png)
进行奇异值分解：
左奇异向量的第一列表示每一个词的出现频繁程度，虽然不是线性的，但是可以认为是一个大概的描述。

右奇异向量中一的第一行表示每一篇文档中的出现词的个数的近似。

![lsi2.png](resources/63365BA9331A7FF0C9C81614447212AC.png)

剩余两维向量在图中表达如下：

![lsi3.png](resources/0B946A644754B58D7E799A82010E2A2B.png)


# 在行推荐系统中的实践
![zaih.png](resources/B6D4FAA553B57B3AA85ED0D614BAB5EB.png)
