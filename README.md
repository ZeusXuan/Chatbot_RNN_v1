# Deep Q&A System

#### Table of Contents

* [Presentation](#presentation)
* [Installation](#installation)
* [Running](#running)
    * [Chatbot](#chatbot)
* [Results](#results)

## Presentation

这个项目主要参考了论文[A Neural Conversational Model](http://arxiv.org/abs/1506.05869),其主要使用了RNN来构建一个用于句子预测的seq2seq模型.

QA System支持以下几种对话语料:
 * [Cornell Movie Dialogs](http://www.cs.cornell.edu/~cristian/Cornell_Movie-Dialogs_Corpus.html) corpus (default). 其已经包含在github库中了.
 * 用户自定义的语料,格式如链接所示 (See [here](data/lightweight) for more info).

为了加速训练需要用到预训练[embeddings layer](data/embeddings).

## Installation

该项目所需的依赖库如下(使用pip进行安装: `pip3 install -r requirements.txt`):
 * python 3.5
 * tensorflow (tested with v1.0)
 * numpy
 * CUDA (for using GPU)
 * nltk (natural language toolkit for tokenized the sentences)
 * tqdm (for the nice progression bars)

## Running

### Chatbot

运行`main.py`进行一次训练. 训练后用 `main.py --test` (结果保存在'save/model/samples_predictions.txt') 或者`main.py --test interactive` (more fun)进行验证.

下面是`main.py`一些有用的选项. 使用`python main.py -h`获取更多帮助:
 * `--modelTag <name>`: 给当前模型取名,以便在训练和测试时进行区分.
 * `--keepAll`: 如果你想在测试时在看到在不同steps的预测结果,在训练时使用该选项 (it can be interesting to see the program changes its name and age as the training progress). Warning: 如果你不加入`--saveEvery` 选项,这会占用大量的存储空间
 * `--filterVocab 20` 和 `--vocabularySize 30000`: 限制词表的大小来优化性能和内存. 这里将出现次数小于20的词设置为 `<unknown>` token 并且设置了最大词表数量为30000.
 * `--verbose`: 测试时打印出正常处理的句子
 * `--playDataset`: 打印一些数据集中的对话(可以与 `--createDataset` 一起使用).

可视化计算图和开销使用 [TensorBoard](https://www.tensorflow.org/how_tos/summaries_and_tensorboard/), 或者直接运行`tensorboard --logdir save/`.

默认情况下, 网络结构为一个enbedding layer和2 LSTM layer (hidden size of 256) 的encoder/decoder, 使用ADAM算法, embedding size为32,输出最大句子长度为10个单词但是可以增大.

## Results

通过缩小模型, 将输入长度限制为5, 输出长度限制为3 (包括了`go` 和 `eos` tokens), embedding size 设置为25, 训练大概需要1到2个小时 (GeForce MX350).
当然, 这个小网络的能力确实一般:

    Q: Hi
    A: Hi.

    Q: What is your name ?
    A: Laura.

    Q: What does that mean ?
    A: I dunno.

    Q: How old are you ?
    A: thirty-five.

    Q: Will Google hire me ?
    A: No.

    Q: Tell me the alphabet
    A: Fuck you.

    Q: That's not nice
    A: Yeah.

下面是一些失败的例子:

    Q: Who is Laura ?
    A: My brother.

    Q: Say 'goodbye'
    A: Alright.

    Q: What is cooking ?
    A: A channel.

    Q: Can you say no ?
    A: No.

    Q: Two plus two
    A: Manny...

输出更长的句子时, 训练的时间明显更长. 在250000 iterations 和 maxLength=10的情况下, 模型还是主要在逃避回答 (大多数问题都直接回答 'I don't know') 但是其还是能捕获句子中的基本语义:

    Q: When are the fireworks ?
    A: Tomorrow.

    Q: What color ?
    A: Gray.

    Q: Which apple ?
    A: That's the first one.

    Q: What time is it ?
    A: Eleven o'clock.

    Q: Who is Skywalker ?
    A: She's a freelance bio-exorcist.

    Q: Who is John ?
    A: The old man.

    Q: Who is Jenny ?
    A: The man who killed herself.

    Q: When were you born ?
    A: Soon.

正如QA例子所示的, 这个网络对性别和现在/过去的语态还是容易混淆, 下面尝试一些较为深刻的哲学问题.

    Q: What is the purpose of being intelligent ?
    A: The man are obsolete.

    Q: What is immoral ?
    A: The truth.

    Q: What is morality ?
    A: The worst.

    Q: What is the purpose of existence ?
    A: It 's private.

    Q: What happens if machines can think ?
    A: I think I'll just lie with you.
