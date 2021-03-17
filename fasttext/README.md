# fasttext

在词袋模型中，把单词当作独立的个体，没有考虑词前后的关系。比如“我喜欢你”和“你喜欢我”，使用词袋模型的话，这两句话是完全一样的。

`n-gram` 是对词袋模型的一种改善，它会关注一个单词的前后关系，比如 `n-gram` 中最常见的 `2-gram` ，就关注单词的前一个词，比如“我爱你”，就可以拆分为“我爱”和“爱你”。

与 `word2ec` 使用词袋模型不同， `fasttext` 使用了 `n-gram` 模型，因此 `fasttext` 可以更有效的表达词前后之间的关系。

安装：

``` shell
pipenv install fasttext==0.9.2
```

## 文本分类

在本教程中，我们主要使用 train_supervised，它返回一个模型对象，并对该对象调用 test 和 predict。这对应于学习(和使用)文本分类器。有关 fastText 的其他功能的介绍，请参阅关于学习[单词向量](https://fasttext.cc/docs/en/unsupervised-tutorial.html)的教程。

### 准备数据

正如在引言中提到的，我们需要标记数据来训练我们的监督分类器。在本教程中，我们感兴趣的是构建一个分类器来自动识别有关烹饪的 stackexchange 问题的主题。让我们从 Stackexchange 的烹饪区下载一些问题的例子，以及相关的标签:

``` shell
wget https://dl.fbaipublicfiles.com/fasttext/data/cooking.stackexchange.tar.gz && tar xvzf cooking.stackexchange.tar.gz
```

文本文件的每一行都包含一个标签列表，后面跟着的是相应文档。所有的标签都以 `__label__` 开头，这是 fastText 识别什么是标签或什么是单词的方法。然后训练模型来预测文档中给出的单词的标签。

在训练第一个分类器之前，我们需要将数据分解为训练数据和验证数据。我们将使用验证集来评估学习过的分类器对新数据的处理能力。

我们的完整数据集包含15404个例子。让我们把它分成12404个例子的训练集和3000个例子的验证集:

``` shell
head -n 12404 cooking.stackexchange.txt > cooking.train
tail -n 3000 cooking.stackexchange.txt > cooking.valid
```

### 第一个分类器
