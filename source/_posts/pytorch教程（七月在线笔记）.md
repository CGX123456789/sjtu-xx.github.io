---
title: pytorch教程（七月在线笔记）
toc: true
date: 2020-03-15 10:09:39
tags:
categories:
- 深度学习
- 基础理论
---

# Pytorch基础
<!--more-->
1. 原位修改
函数后加下划线“`_`”表示原位修改:`y.add_(x)`。
2. numpy和tensor的转化
numpy转换为tensor-》`torch.from_array(a)`
tensor转换为numpy-》`a.numpy()`
3. cuda向量
`a.cuda()`
`a.to("cuda")`
`a.to(torch.device("cuda"))`
`torch.ones_like(device=torch.device("cuda"))`
4. 简单的双层神经网络案例
```python
import torch
import torch.nn as nn

# N is batch size; D_in is input dimension;
# H is hidden dimension; D_out is output dimension.
N, D_in, H, D_out = 64, 1000, 100, 10
x = torch.randn(N,D_in)
y = torch.randn(N,D_out)

model = nn.Sequential(
    nn.Linear(D_in,H),
    nn.ReLU(),
    nn.Linear(H,D_out))

loss_fn = nn.MSELoss(reduction="sum")
learning_rate = 1e-4
optimizer = torch.optim.Adam(model.parameters(),lr=learning_rate)

for i in range(500):
    # forward：构建模型
    y_pred = model(x)
    
    # loss
    loss = loss_fn(y_pred,y)
    
    # 默认情况下grad是叠加，zero_grad用来重置参数列表
    optimizer.zero_grad()
    
    # backward：计算梯度
    loss.backward()
    optimizer.step()
    print(i,":",loss.item())
```

```python
# 注意：验证时要no_grad
with torch.no_grad():
    testY = model(testX)
```
5. FizzBuzz游戏简单案例
游戏规则如下：从1开始往上数数，当遇到3的倍数的时候，说fizz，当遇到5的倍数，说buzz，当遇到15的倍数，就说fizzbuzz，其他情况下则正常数数。

## Pytorch模型的建立
模型的建立包括几个步骤：
1. 处理数据
2. 搭建模型
3. forward pass
4. optimizer
5. backward pass

# 词向量和语言模型
## 词向量
[github](https://github.com/sjtu-xx/pytorch_course/blob/master/notebooks/2.word-embedding.ipynb)
资料地址 链接: https://pan.baidu.com/s/1QEFLWKRkMh-wh2nE5kD4Xw 提取码: x8iw
分布式表示：用一个词附近的其他词来表示该词。
Word2Vec：Skip-Gram
![](1.png)

## DataLoader
**实现Dataloader**
一个dataloader需要以下内容：
 - 把所有text编码成数字，然后用subsampling预处理这些文字。
 -  保存vocabulary，单词count，normalized word frequency
 -  每个iteration sample一个中心词
 -  根据当前的中心词返回context单词
 -  根据中心词sample一些negative单词
 -  返回单词的counts
这里有一个好的tutorial介绍如何使用[PyTorch dataloader](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html). 为了使用dataloader，我们需要定义以下两个function:

__len__ function需要返回整个数据集中有多少个item
__getitem__ 根据给定的index返回一个item
有了dataloader之后，我们可以轻松随机打乱整个数据集，拿到一个batch的数据等等。

```python
class WordEmbeddingDataset(tud.Dataset):
    def __init__(self,text,word_to_idx,idx_to_word,word_freqs):
        super(WordEmbeddingDataset,self).__init__()
        self.word_to_idx = word_to_idx
        self.idx_to_word = idx_to_word
        self.word_freqs = torch.Tensor(word_freqs)
        self.text_encoded = torch.Tensor([self.word_to_idx.get(w,self.word_to_idx["<unk>"]) for w in text])
    
    def __len__(self):
        return len(self.text_encoded)
    
    def __getitem__(self,idx):
        center_word = self.text_encoded[idx]
        pos_indices = list(range(idx-C,idx))+list(range(idx+1,idx+C+1))
        pos_indices = [i%len(self.word_to_idx) for i in pos_indices]
        pos_words = torch.Tensor([self.text_encoded[i] for i in pos_indices])
        neg_words = torch.multinomial(self.word_freqs,K*pos_words.shape[0],True)
        return center_word,pos_words,neg_words

dataset = WordEmbeddingDataset(text,word_to_idx,idx_to_word,word_freqs)
dataloader = tud.DataLoader(dataset,batch_size=BATCH_SIZE,shuffle=True)
```

## model
```python
class EmbeddingModel(nn.Module):
    def __init__(self,vocab_size,embed_size):
        super(EmbeddingModel,self).__init__()
        self.vocab_size = vocab_size
        self.embed_size = embed_size
        
        self.in_embed = nn.Embedding(self.vocab_size,self.embed_size)
        self.out_embed = nn.Embedding(self.vocab_size,self.embed_size)
        
    def forward(self,input_label,pos_labels,neg_labels):
        # input_label : [batch_size]
        # pos_label : [batch_size,window_size*2]
        # neg_label : [batch_size,window_size*2*K]
        input_embed = self.in_embed(input_label) # [batch_size,embed_size]
        pos_embed = self.out_embed(pos_labels) # [batch_size,window_size*2,embed_size]
        neg_embed = self.out_embed(neg_labels) # [batch_size,window_size*2*K,embed_size]
        
        input_embed = input_embed.unsqueeze(2)
        pos_dot = torch.bmm(pos_embed,input_embed).squeeze(2) # [batch_size,window_size*2]
        neg_dot = torch.bmm(neg_embed,-input_embed).squeeze(2) # [batch_size,window_size*2*K]
        
        pos_loss = F.logsigmoid(pos_dot).sum(1) #.sum(1)
        neg_loss = F.logsigmoid(neg_dot).sum(1)
        
        loss = pos_loss+neg_loss
        return -loss
    
    def input_embeddings(self):
        return self.in_embed.weight.data.cpu().numpy()
```

## training
```python
model = EmbeddingModel(VOCAB_SIZE,EMBEDDING_SIZE)
if USE_CUDA:
    model = model.cuda()
optimizer = torch.optim.SGD(model.parameters(),lr=LEARNING_RATE)
for e in range(NUM_EPOCHS):
    for i,(input_label,pos_labels,neg_labels) in enumerate(dataloader):
        if USE_CUDA:
            input_label = input_label.long().cuda()
            pos_labels = pos_labels.long().cuda()
            neg_labels = neg_labels.long().cuda()
            
        optimizer.zero_grad()
        loss = model(input_label,pos_labels,neg_labels).mean()
        loss.backward()
        optimizer.step()
        if i % 100 == 0:
            print("epoch: {}, iter: {}, loss: {}".format(e, i, loss.item()))
```

## 评估
```python
def evaluate(filename, embedding_weights): 
    if filename.endswith(".csv"):
        data = pd.read_csv(filename, sep=",")
    else:
        data = pd.read_csv(filename, sep="\t")
    human_similarity = []
    model_similarity = []
    for i in data.iloc[:, 0:2].index:
        word1, word2 = data.iloc[i, 0], data.iloc[i, 1]
        if word1 not in word_to_idx or word2 not in word_to_idx:
            continue
        else:
            word1_idx, word2_idx = word_to_idx[word1], word_to_idx[word2]
            word1_embed, word2_embed = embedding_weights[[word1_idx]], embedding_weights[[word2_idx]]
            model_similarity.append(float(sklearn.metrics.pairwise.cosine_similarity(word1_embed, word2_embed)))
            human_similarity.append(float(data.iloc[i, 2]))

    return scipy.stats.spearmanr(human_similarity, model_similarity)# , model_similarity

def find_nearest(word):
    index = word_to_idx[word]
    embedding = embedding_weights[index]
    cos_dis = np.array([scipy.spatial.distance.cosine(e, embedding) for e in embedding_weights])
    return [idx_to_word[i] for i in cos_dis.argsort()[:10]]
```

# 语言模型
## 常见的语言模型
### RNN（Recurrent neural network）
### Long short-term memory(LSTM)：
![](4.png)
几个门来控制输入输出：
忘记门、输入门、更新门、输出门
原始的LSTM：
![](2.png)
优化的LSTM（Pytorch中的LSTM）：
![](3.png)

训练时训练CrossEntropy
### Gated Recurrent unit
![](5.png)

## 模型建立
这些循环神经网络都可能出现梯度爆炸的问题。因此最好做一个gradient clipping
[github](https://github.com/sjtu-xx/pytorch_course/blob/master/notebooks/3.language-model.ipynb)
1. 文本处理
```python
TEXT = torchtext.data.Field(lower=True)
train, val, test = torchtext.datasets.LanguageModelingDataset.splits(path=".", 
    train="word2vec/text8.train.txt", validation="word2vec/text8.dev.txt", test="word2vec/text8.test.txt", text_field=TEXT)
TEXT.build_vocab(train, max_size=MAX_VOCAB_SIZE)
print("vocabulary size: {}".format(len(TEXT.vocab)))

VOCAB_SIZE = len(TEXT.vocab)
TEXT.vocab.itos # index to string. type:list 包含两个特殊字符<unk> <pad>
TEXT.vocab.stoi # string to index. type:dict 包含两个特殊字符<unk> <pad>
train_iter, val_iter, test_iter = torchtext.data.BPTTIterator.splits(
    (train, val, test), batch_size=BATCH_SIZE, device=-1, bptt_len=32, repeat=False, shuffle=True)
```
2. 模型保存
```python
    val_loss = evaluate(model, val_iter)
    
    if len(val_losses) == 0 or val_loss < min(val_losses):
        print("best model, val loss: ", val_loss)
        torch.save(model.state_dict(), "lm-best.th")
    else:
        scheduler.step()
        optimizer = torch.optim.Adam(model.parameters(), lr=learning_rate)
    val_losses.append(val_loss) # scheduler = torch.optim.lr_scheduler.ExponentialLR(optimizer, 0.5)
```
3. 模型加载
```python
best_model = RNNModel("LSTM", VOCAB_SIZE, EMBEDDING_SIZE, EMBEDDING_SIZE, 2, dropout=0.5)
if USE_CUDA:
    best_model = best_model.cuda()
best_model.load_state_dict(torch.load("lm-best.th"))
```

## 文本分类模型
![](6.png)
![](7.png)
![](8.png)
![](9.png)

logits : 任何数字
sigmoid处理后： probability

BCEWithLogitsLoss()  binary cross entropy: 二分类问题
文本分类模型中，dropout很重要，特别是数据量比较少的时候。（可以dropout，或把部分单词的word调成`<unk>`）
WordAVG 很强。

# 卷积网络
local feature detect
> Batchnormalization
> ![](16.png)
## 常见的卷积网络
![](11.png)
![](12.png)
![](13.png)
![](14.png)
![](15.png)

## 迁移学习
我们常用以下两种方法做迁移学习。
- fine tuning: 从一个预训练模型开始，我们改变一些模型的架构，然后继续训练整个模型的参数。
- feature extraction: 我们不再改变预训练模型的参数，而是只更新我们改变过的部分模型参数。我们之所以叫它feature extraction是因为我们把预训练的CNN模型当做一个特征提取模型，利用提取出来的特征做来完成我们的训练任务。
通过requires_grad = False 来冻结层
**以下是构建和训练迁移学习模型的基本步骤**：
- 初始化预训练模型
- 把最后一层的输出层改变成我们想要分的类别总数
- 定义一个optimizer来更新参数
- 模型训练

# 图片风格迁移和GAN
## 风格迁移
**content loss**
![](17.png)
**style**
Gram Matrix可以被拿来表示两张图片的texture相似度
![](18.png)
**style loss**
![](19.png)
## GAN(Generative Adversarial Networks)
Generator: 生成器，目标是让生成的数据接近真实数据
Discriminator: 判别器，目标是能够鉴别真实数据和生成的假数据

论文：style transfer from non-parallel text by cross-alignment

## DCGAN
使用deconv层作为图片生成器
Deconvolutional Layer https://datascience.stackexchange.com/questions/6107/what-are-deconvolutional-layers
介绍deconv
https://arxiv.org/pdf/1603.07285.pdf

# Seq2Seq和Attention
![](20.png)
![](21.png)
![](22.png)

# 大规模预训练语言模型
https://zhuanlan.zhihu.com/p/46652512
BERT、ELMO、OpenAI GPT

## ELMO
一个预训练两层双向LSTM语言模型：-》得到的是词向量->用来替代embedding层，通常能提高很多
https://www.aclweb.org/anthology/N18-1202
https://github.com/allenai/allennlp
![](23.png)
![](24.png)
**AllenNLP**
一个很好的构建NLP模型的package，基于PyTorch
AllenAI在2018 EMNLP上的一个tutorial
https://github.com/allenai/writing-code-for-nlp-research-emnlp2018/blob/master/writing_code_for_nlp_research.pdf

elmo的训练代码：[bilm-tf](https://github.com/allenai/bilm-tf)
elmo使用tensorflow训练再导出到pytorch中

## BERT
不是一个语言模型，目标是预测masked word

## OpenAI GPT-2
一种[Transformer模型](https://zhuanlan.zhihu.com/p/39034683)