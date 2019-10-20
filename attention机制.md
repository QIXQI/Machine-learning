## Dynamic conditional generation

注意力机制

RNN

***

动态生成条件

对比：

* Past:  Encoder 的每个时间点的output都给Decoder, Decoder的input 是整个句子的信息
* Now: Decoder 看到的 information 在每个时间点都不一样

好处：

* Encoder 句子复杂的话，没有办法使用一个 vector 表示
* Decoder 可以值考虑需要的信息



## eg

### 机器翻译



```mermaid
graph TD
	机 -->|向量|h1
	器 -->|向量|h2
	学 -->|向量|h3
	习 -->|向量|h4
	h1 -->|z0|match匹配相似度
	h2 -->|z0|match匹配相似度
	h3 -->|z0|match匹配相似度
	h4 -->|z0|match匹配相似度
	match匹配相似度 -->a01
	match匹配相似度 -->a02
	match匹配相似度 -->a03
	match匹配相似度 -->a04
	a01 -->softmax:映射到0-1,归一化后和为1,常见于多分类问题中
	a02 -->softmax:映射到0-1,归一化后和为1,常见于多分类问题中
	a03 -->softmax:映射到0-1,归一化后和为1,常见于多分类问题中
	a04 -->softmax:映射到0-1,归一化后和为1,常见于多分类问题中
	softmax:映射到0-1,归一化后和为1,常见于多分类问题中 --> a01'
	softmax:映射到0-1,归一化后和为1,常见于多分类问题中 --> a02'
	softmax:映射到0-1,归一化后和为1,常见于多分类问题中 --> a03'
	softmax:映射到0-1,归一化后和为1,常见于多分类问题中 --> a04'
	a01' -->c0
	a02' -->c0
	a03' -->c0
	a04' -->c0

	

```

每个时间点每个词汇都可以用一个向量来表示

其中 $z_{0}$ 先随机初始化，然后学习得到，也是向量

$c^{0} = \sum \hat{a}_{0}^{i}h^{i}$

比如，$\hat{\alpha}^{1}_{0} = 0.5, \hat{\alpha}^{2}_{0} = 0.5, \hat{\alpha}^{3}_{0} = 0.0, \hat{\alpha}^{4}_{0} = 0.0$，则 $c_{0} = \hat{\alpha}^{1}_{0}h^{1} + \hat{\alpha}^{1}_{0}h^{1}$，则$h^{1} , h^{2}$ 对应的词汇导入 decoder，即"机器"，输出"machine"

* Match，没有参数，导入$z_{0}$与$h^{1}$ ，返回相似度
* Match，有参数，通过学习得到的矩阵$W$ , $\alpha = h^{T}Wz$



*Decoder*

```mermaid
graph BT
	z0 -->|RNN的输出,相当于下一个要关注的地方,然后再做相似度|z1
	z1 -->z2
	z2 -->|...|直到句号结束
	subgraph two
		B[0.5h3 + 0.5h4] -->|学习|z2
		z2 -->'learning'
	end
	subgraph one
		A[c0 = 0.5h1 + 0.5h2] -->|机器|z1
		z1 -->'machine'
	end


	
	
```

### 语音识别

输入：声音讯号，每个很小的时间间隔用向量表示

```mermaid
graph LR
	A[向量] -->|match function|B[相似度]
	B -->|match 值高的地方丢入Decoder|C['h']
	C -->|'o','y',' ','a','r','e',' ','y','o','u','?'|D[识别出一句话]
```

效果没有想过传统方法



### 图像字幕

将图片翻译为一段描述性的文字

一张图片用一组CNN中的filter描述图片，因为每一个filter的输出是一个向量

```mermaid
graph LR
	z0 -->|match function|A[每一个向量的相关度]
	A -->|softmax|B[权重]
	B -->|通过权重将这几个向量求和|C[weighted_sum向量]
	C -->|丢入Decoder|z1
	z1 -->|word1|z2
	z2 -->|word2|...
```

### 视频字幕

将视频分为图片帧