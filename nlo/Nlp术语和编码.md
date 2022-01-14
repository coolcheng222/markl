# NLP术语和编码

## 一. 单词含义(1)

### 1. 简单绪论

1. 基于同义词词典
2. 基于计数
3. 基于推理word2vec

### 2. 同义词词典

同义词词典包含了单词之间的关系,有同义词关系,整体部分关系等

可以形成单词网络

* 示例:
  * `WordNet`
* 缺陷:
  * 应对改变或者自造词无法接受
  * 人力成本高
  * 无法表示单词间的微妙差异

### 3. 计数

基于`语料库`corpus,即收集大量的文本数据

#### 3.1 语料库

* 语料库制作示例:

  ```python
  # 作为示例句子
  text = "You say goodbye and I say Hello."
  # 预处理句子
  text.lower()
  text = text.replace("."," .")
  
  # 分出word列表
  words = text.split(' ')
  # re.split('(\W+)?',text)
  
  # 两个列表方便使用,主要是使用id_to_word训练
  word_to_id = {}
  id_to_word = {}
  
  for word in words:
      if word not in word_to_id:
          new_id = len(word_to_id)
          word_to_id[word] = new_id
          id_to_word[new_id] = word
  # {0: 'You', 1: 'say', 2: 'goodbye', 3: 'and', 4: 'I', 5: 'Hello', 6: '.'}
  ```

* 语料库将文本转化为输入

  ```python
  import numpy as np 
  corpus = [word_to_id[w] for w in words]
  corpus = np.array(corpus)
  ```

  

#### 3.2 分布式文字表示(表示为向量)

如同RGB矢量表示颜色,我们也希望用一种矢量表示word

`分布式假设`: 某个单词含义由它周围的单词组成

`context`: 上下文,指所考虑单词周围的单词

`window size`: 选择左右的单词数量(一边)

#### 3.3 共现矩阵

表示词语上下文共现单词的频数的矩阵

行列都为语料列表,W(A,B)表示A上下文中出现B的频数

该__行向量__就是表示对应单词的矢量(即用上下文关系定义一个word)

```python
def create_co_matrix(corpus,vocab_size,window_size=1):
    corpus_size = len(corpus)
    co_matrix = np.zeros((vocab_size,vocab_size),dtype=np.int32)

    for idx,word_id in enumerate(corpus):
        for i in range(1,window_size + 1):
            left_idx = idx - i
            right_idx = idx + i
            if left_idx >= 0:
                left_word_id = corpus[left_idx]
                co_matrix[word_id,left_word_id] += 1
            if right_idx < corpus_size:
                right_word_id = corpus[right_idx]
                co_matrix[word_id,right_word_id] += 1
    return co_matrix

```

#### 3.4 向量之间的相似度实现

相似度的实现可以是向量之间的余弦值,即`点积/长度`

```python
def cos_similiarity(x,y,eps = 1e-8):
    nx = x / (np.sqrt(np.sum(x**2)) + eps)
    ny = y / (np.sqrt(np.sum(y ** 2)) + eps)
    return np.dot(nx,ny)
```

#### 3.附 相似向量的倒序排序

```python
def most_similar(query,word_to_id,id_to_word,word_matrix,top=5):
    if query not in word_to_id:
        print(f'{query} is not found')
        return 
    print('\n[query]' + query)
    query_id = word_to_id[query]
    query_vec = word_matrix[query_id]

    vocab_size = len(id_to_word)
    similiarity = np.zeros(vocab_size)
    for i in range(vocab_size):
        similiarity[i] = cos_similiarity(word_matrix[i],query_vec)
    
    count = 0
    # 倒序排序,argsort返回索引
    for i in (-1 * similiarity).argsort():
        if id_to_word[i] == query:
            continue
        print(f'{id_to_word[i]}:{similiarity[i]}')
        count += 1
        if count >= top:
            return
```





### 4. 计数plus

#### 4.1 计数存在的问题和改进

上述计数没有对语料库中可能出现的频数做处理,频数越多,相似度越大

我们采用`点互信息(Pointwise Mutual Information)`

#### 4.2 PMI相关系数

$$
PMI(x,y) = log_2\frac{P(x,y)}{P(x),P(y)}
$$

PMI表达了一种相关系数,PMI越高相关性越强

在语料库和共现矩阵下,PMI为:
$$
PMI(x,y) = log_2\frac{C(x,y)N}{C(x)C(y)}
$$
C(x)指出现的频数,C(x,y)指共现值

为防止出现共现为0的情况,我们定义`positive pmi=PPMI`
$$
PPMI(x,y) = max(0,PMI(x,y))
$$

```python
def ppmi(C,verbose = False,eps=1e-8):
    M = np.zeros_like(C,dtype=np.float32)
    N = np.sum(C)
    S = np.sum(C,axis = 0) # 共现中的频数

    total = C.shape[0] * C.shape[1]
    cnt = 0

    for i in range(C.shape[0]):
        for j in range(C.shape[1]):
            pmi = np.log2(C[i,j] * N / (S[j] *S[i] + eps))
            M[i,j] = max(0,pmi)

    return M
```

#### 4.3 降维(SVD)

二维数组形成的PPMI矩阵终究扛不住大语料库

* 思路: 发现矢量中存在的轴,并用轴上的投影表示各个矢量的值,形成二维到一维的降维

* `奇异值分解(SVD)`:

  * 将任意矩阵分解为3个矩阵的乘积
    $$
    X = USV^T
    $$
    U和V彼此正交,S为对角矩阵,都不一定是方阵

    性质:

    * S的矩阵值降序排列,且衰减很快
    * 所以我们可以用**U的前几列**来表示一个PPMI向量

numpy的实现:

```python
U,S,V = np.linalg.svd(M)
```

sklearn的实现(更快):

```python
from sklearn.utils.extmath import randomized_svd
U,S,V = randomized_svd(W,n_components=wordvec_size,n_iter=5,random_state=None)
```



### 附. PTB语料库介绍

PTB语料库的处理:

* 将稀有字符替换成`<unk>`
* 将数字替换为`N`
* 每一行一个句子,在句尾插入`<eos>`

## 二. word2vec

`基于推理`

### 1. 基于计数的问题

1. 对于大规模语料库进行SVD还是不现实

### 2. 基于推理的思想

__推理__: 当给出单词上下文,根据上下文推理该单词的过程就是推理

__分布式假设__: 一个单词由周围的单词构成

基于此,我们可以使用__神经网络__进行推理,<u>输入为上下文,输出为各个单词的概率</u>

* word2vec中的单词表示:
  * `one-hot`形成一个词汇表大小的输入

### 3. word2vec

我们使用`continuous bag-of-words(CBOW)`模型作为word2vec的神经网络

#### 3.1 CBOW的结构

CBOW输入上下文,对于考虑N个单词上下文的情况,输入层为N个,每层输入一个one-hot向量

* 多个输入层的处理:
  * 假设第i个输入层变换后为$h_i$,则中间层的神经元的输入为$\frac{\Sigma h_i}{N}$
  * 每个输入层到中间层之间共享一个$W_{in}$,该权重对应的就是单词的向量表示
  * 不使用bias
* 中间层特点:
  
* 节点数量少,因为其节点数量决定了$W_{in}$的宽度
  
* 一次推理过程编码示范:

  ```python
  # 模拟两个输入
  c0 = np.array([[1,0,0,0,0,0,0]])
  c1 = np.array([[0,0,1,0,0,0,0]])
  
  # 两层的权重
  W_in = np.random.randn(7,3)
  W_out = np.random.randn(3,7)
  
  # 两个输入对应的层
  in_layer0 = MatMul(W_in)
  in_layer1 = MatMul(W_in)
  # 输出层
  out_layer = MatMul(W_out)
  
  h0 = in_layer0.forward(c0)
  h1 = in_layer1.forward(c1)
  
  # 中间层
  h = 0.5 * (h0 + h1)
  # 输出层
  s = out_layer.forward(h)
  
  ```

#### 3.2 CBOW的学习

要素1: 使用softmax得到最后的概率,使用Cross-entropy得到loss

* 预处理:

  * 提取上下文和目标词:

    * 目标词应保证语料库中有上下文可以预测,所以从window_size开始

    ```python
    # 上下文是一个二维的列表
    # 目标词是一个对应context0维的数组
    
    def create_contexts_target(corpus):
        target = corpus[windows_size:-windows_size]
        contexts = []
    	
        # 从左到右遍历目标词
        for idx in range(window_size,len(corpus) - window_size):
            cs = []
            # 把左右上下文加入列表
            for t in range(-window_size,window_size + 1):
                if t == 0:
                    continue
            	cs.append(corpus[idx + t])
            contexts.append(cs)
        return np.array(contexts),np.array(target)
    ```

    

  * 将context和target转换为one-hot:

    * context每个id都展开,成为3d矩阵
    * target展开变为2d矩阵

    ```python
    def convert_one_hot(corpus, vocab_size):
        '''转换为one-hot表示
    
        :param corpus: 单词ID列表（一维或二维的NumPy数组）
        :param vocab_size: 词汇个数
        :return: one-hot表示（二维或三维的NumPy数组）
        '''
        N = corpus.shape[0]
    
        if corpus.ndim == 1:
            one_hot = np.zeros((N, vocab_size), dtype=np.int32)
            for idx, word_id in enumerate(corpus):
                one_hot[idx, word_id] = 1
    
        elif corpus.ndim == 2:
            C = corpus.shape[1]
            one_hot = np.zeros((N, C, vocab_size), dtype=np.int32)
            for idx_0, word_ids in enumerate(corpus):
                for idx_1, word_id in enumerate(word_ids):
                    one_hot[idx_0, idx_1, word_id] = 1
    
        return one_hot
    ```

    

    

#### 3.3 word2vec中分布式表示的选择

word2vec中有两个权重,我们可以从权重中选择单词的向量表示

>  选择1: 输入侧权重
>
> 选择2: 输出侧权重
>
> 选择3: 同时使用(比如相加)

其中选择1更受欢迎和有效

#### 3.4 CBOW模型的实现

初始化:

```python
class SimpleCBOW:
    def __init__(self,vocab_size,hidden_size):
        V,H = vocab_size,hidden_size

        # 生成权重
        W_in = 0.01 * np.random.randn(V,H).astype('f')
        W_out = 0.01 * np.random.randn(H,V).astype('f')
        # 生成layer
        self.in_layer0 = MatMul(W_in)
        self.in_layer1 = MatMul(W_in)
        self.out_layer = MatMul(W_out)
        self.loss_layer = SoftmaxWithLoss()

        layers = [self.in_layer0,self.in_layer1,self.out_layer]
        self.params,self.grads = [],[]
        # 初始化params和grads
        for layer in layers:
            self.params += layer.params
            self.grads += layer.grads
        # 将word vec设为输入W
        self.word_vecs = W_in
```

预测:

```python
def forward(self,contexts,target):
    h0 = self.in_layer0.forward(contexts[:,0])
    h1 = self.in_layer1.forward(contexts[:,1])

    h = 0.5 * (h0 + h1)
    score = out_layer.forward(h)
    loss = self.loss_layer.forward(score,target)
```

反向传播:

```python
 def backward(self,dout=1):
        ds = self.loss_layer.backward(dout)
        da = self.out_layer.backward(ds)
        da *= 0.5
        self.in_layer1.backward(da)
        self.in_layer0.backward(db)
        return None
```

训练:

```python
window_size = 1
hidden_size = 5
batch_size = 3
max_epoch = 1000

text = 'You say goodbye and I say hello.'
corpus,word_to_id,id_to_word = preprocess(text)

vocab_size = len(word_to_id)
contexts,target = create_contexts_target(corpus,window_size)
target = convert_one_hot(target,vocab_size)
contexts = convert_one_hot(contexts,vocab_size)

model = SimpleCBOW(vocab_size,hidden_size)
optimizer = Adam()
trainer = Trainer(model,optimizer)
trainer.fit(contexts,target,max_epoch,batch_size)
trainer.plot()
```

<img src="../pics/Nlp术语和编码/image-20220112144904851.png" alt="image-20220112144904851" style="zoom:50%;" />

### 附. 补充说明

我们看到,CBOW没有激活,其实就是在向量中线性表示概率

`skip-gram`属于是反向CBOW,根据目标词猜上下文

一个W_in一个W_out,输出表示多重含义

## 三. word2vec的改进

当前实现简单,但是对于大词汇量还是太慢

### 1. 瓶颈

1. 输入层的one-hot表示 与 W_in的乘积
2. 中间层和W_out的乘积
3. softmax的计算

### 2. embedding

我们注意到,一个one-hot乘以一个W其实就是将其中一行提取出来

所以我们不需要矩阵乘法,我们只需要提取一行即可

称为`Embedding层`,来自于 word embedding,即单词的密集向量表示法

实现后替代MatMul层即可

> 注意: one-hot也不需要了,一开始传入id即可

```python

def Embedding:
    def __init__(self,W):
        self.params = [W]
        self.grads = [np.zeros_like(W)]
        self.idx = None
    def forward(self,idx):
        # idx是一个列表,可以一次提取多行
        W, = self.params
        self.idx = idx
        # 提取一行
        out = W[idx]
        return outs
```

反向传播思路就是将其他行置0,只有提取的行赋值dout * 1

```python
  def backward(self,dout):
        dW = self.grads
        dW[...] = 0
        # 不赋值而相加是因为idx可能出现重复
        for i,word_id in enumerate(self.idx):
            dW[word_id] += dout[i]
        return None
```

### 3. negative sampling

用于简化中间层的矩阵乘法和softmax计算

#### 3.1 思想

* 从多分类到二分类
  * 用二分类拟合多分类: 把选单词变为询问是否单词
  * 该单词即我们需要的__目标词__
* 处理:
  * 假设我们只关注其中一个单词,我们就可以用Sigmoid取代softmax
  * Sigmoid和Cross Entropy的组合,反向传播依然为y-t
  * 将中间层的输出转化为__输入层的输出点乘W_out的列Embedding__

#### 3.2 Embedding dot的实现

```python
def EmbeddingDot:
    def __init__(self,W):
        self.embed = Embedding(W)
        self.params = self.embed.params
        # 对于W只需要embed的grads
        self.grads = self.embed.grads
        self.cache = None
    def forward(self,h,idx):
        # 抽取embed
        target_W = self.embed.forward(idx)
        # 向量乘积
        out = np.sum(target_W * h,axis=1)
        self.cache = (h,target_W)
        return out
    def backward(self,dout):
        h,target_W = self.cache
        # 展开
        dout = dout.reshape(dout.shape[0],1)

        # W的值,h
        dtarget_W = dout * h
        # 再经过embed,更新grads
        self.embed.backward(dtarget_W)
        dh = dout * target_W
        return dh
```

#### 3.3 负采样

之前的例子隐藏着一个**问题**: 我们只关注了正确结果的数值高,但是没有保证其他错误结果的数值低

* 解决方案: 负采样

  * 我们不需要采用所有的负例,我们只需要挑选若干
  * 这些词的__loss和正例loss的总和__可以作为最后的loss'

  <img src="../pics/Nlp术语和编码/image-20220113150201865.png" alt="image-20220113150201865" style="zoom:33%;" />

反向传播时回直接传播一个dout作为y,对于不同解标签有不同行为

* 采样方法:

  * 按照频率作为概率分布采样

    ```python
    np.random.choice(words,p=p) # p用于确定概率分布
    ```

    * 概率分布处理: 0.75次方提高低频词概率
      $$
      \frac{P(W_i)^{0.75}}{\Sigma P(w_i)^{0.75}}
      $$

      ```python
      new_p = np.power(p,0.75)
      new_p /= np.sum(new_p)
      ```

* 采样实现

  ```python
  class UnigramSampler:
      def __init__(self, corpus, power, sample_size):
          self.sample_size = sample_size
          self.vocab_size = None
          self.word_p = None
  
          counts = collections.Counter()
          for word_id in corpus:
              # 频数统计
              counts[word_id] += 1
  
          vocab_size = len(counts)
          self.vocab_size = vocab_size
  
          self.word_p = np.zeros(vocab_size)
          for i in range(vocab_size):
              self.word_p[i] = counts[i]
  
          self.word_p = np.power(self.word_p, power)
          self.word_p /= np.sum(self.word_p)
  
      def get_negative_sample(self, target):
          batch_size = target.shape[0]
  
          if not GPU:
              negative_sample = np.zeros((batch_size, self.sample_size), dtype=np.int32)
  
              for i in range(batch_size):
                  p = self.word_p.copy()
                  target_idx = target[i]
                  p[target_idx] = 0
                  p /= p.sum()
                  negative_sample[i, :] = np.random.choice(self.vocab_size, size=self.sample_size, replace=False, p=p)
          else:
              # 在用GPU(cupy）计算时，优先速度
              # 有时目标词存在于负例中
              negative_sample = np.random.choice(self.vocab_size, size=(batch_size, self.sample_size),
                                                 replace=True, p=self.word_p)
  
          return negative_sample
  
  ```

* 负采样实现

  * 初始化:

    ```python
    class NagativceSamplingLoss:
        def __init__(self,W,corpus,power=0.75,sample_size = 5):
            self.sample_size = sample_size
            self.sampler = UnigramSampler(corpus,power,sample_size)
            # 重复sample遍
            self.loss_layers = [SigmoidWithLoss() for _ in range(sample_size + 1)]
            self.embed_dot_layers = [EmbeddingDot(W) for _ in range(sample_size + 1)]
            self.params,self.grads = [],[]
            for layer in self.embed_dot_layers:
                self.params += layer.params
                self.grads += layer.grads
    ```

  * 正向传播:

    ```python
    def forward(self,h,target):
        batch_size = target.shape[0]
        negative_sample = self.sampler.get_negative_sample(target)
    
        # 不关乎embed的顺序,target即为idx,决定Wout的列
        score = self.embed_dot_layers[0].forward(h,target)
        correct_label = np.ones(batch_size,dtype=np.int32)
        # 正确取1
        loss = self.loss_layers[0].forward(score,correct_label)
    
        negative_label = np.zeros(batch_size,dtype=np.int32)
        for i in range(self.sample_size):
            negative_target = negative_sample[:,i]
            score = self.embed_dot_layers[1 + i].forward(h,negative_target)
            loss += self.loss_layers[1 + i].forward(score,negative_label)
        return loss
    ```

  * 反向传播:

    ```python
    def backward(self,dout):
        dh = 0
        for l0,l1 in zip(self.loss_layers,self.embed_dot_layers):
            dscore = l0.backward(dout)
            dh += l1.backward(dscore)
        return dh
    ```

### 4. 最终版CBOW

```python

class CBOW:
    def __init__(self, vocab_size, hidden_size, window_size, corpus):
        V, H = vocab_size, hidden_size

        # 初始化权重
        W_in = 0.01 * np.random.randn(V, H).astype('f')
        W_out = 0.01 * np.random.randn(V, H).astype('f')

        # 生成层
        self.in_layers = []
        # window size * 2有多大就整多少个Embedding
        for i in range(2 * window_size):
            layer = Embedding(W_in)  # 使用Embedding层
            self.in_layers.append(layer)
        # 输出层-sigmoid-loss层使用负采样一次性解决
        self.ns_loss = NegativeSamplingLoss(W_out, corpus, power=0.75, sample_size=5)

        # 将所有的权重和梯度整理到列表中
        layers = self.in_layers + [self.ns_loss]
        self.params, self.grads = [], []
        for layer in layers:
            self.params += layer.params
            # 以数组拼接,跟随layer.grads变化
            self.grads += layer.grads

        # 将单词的分布式表示设置为成员变量
        self.word_vecs = W_in

    def forward(self, contexts, target):
        h = 0
        for i, layer in enumerate(self.in_layers):
            h += layer.forward(contexts[:, i])
        h *= 1 / len(self.in_layers)
        loss = self.ns_loss.forward(h, target)
        return loss

    def backward(self, dout=1):
        dout = self.ns_loss.backward(dout)
        dout *= 1 / len(self.in_layers)
        for layer in self.in_layers:
            layer.backward(dout)
        return None

```

* 训练代码

  ```python
  
  # 设定超参数
  window_size = 5
  hidden_size = 100
  batch_size = 100
  max_epoch = 10
  
  # 读入数据
  corpus, word_to_id, id_to_word = ptb.load_data('train')
  vocab_size = len(word_to_id)
  
  contexts, target = create_contexts_target(corpus, window_size)
  if config.GPU:
      contexts, target = to_gpu(contexts), to_gpu(target)
  
  # 生成模型等
  model = CBOW(vocab_size, hidden_size, window_size, corpus)
  # model = SkipGram(vocab_size, hidden_size, window_size, corpus)
  optimizer = Adam()
  trainer = Trainer(model, optimizer)
  
  # 开始学习
  trainer.fit(contexts, target, max_epoch, batch_size)
  trainer.plot()
  
  # 保存必要数据，以便后续使用
  word_vecs = model.word_vecs
  if config.GPU:
      word_vecs = to_cpu(word_vecs)
  params = {}
  params['word_vecs'] = word_vecs.astype(np.float16)
  params['word_to_id'] = word_to_id
  params['id_to_word'] = id_to_word
  pkl_file = 'cbow_params.pkl'  # or 'skipgram_params.pkl'
  with open(pkl_file, 'wb') as f:
      pickle.dump(params, f, -1)
  
  ```

  