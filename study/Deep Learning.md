Deep Learning——从入门到放弃
=======
为大家介绍一些神经网络/深度学习的知识，拉大家一起入坑，和大家共同提高，一起从入门到放弃。<br>
一开始先介绍一些常识或工具，推荐一些文献，之后再慢慢扩充，介绍更详细的知识，请大家多提建议【虽然不一定会听】ヾ(◍°∇°◍)ﾉﾞ。<br>
## 什么是深度学习？<br>
深度学习（Deep Learning，下文有时会简称为DL）是机器学习的一种，其概念由大神`Geoffrey Hinton`于2006年借深度信念网络（Deep Belief Nets，DBN）提出。DL说白了其实就是一种分类方法，也可以看成一种算法，其原理和传统机器学习虽然不一样，但作用是一样的。<br>
其实业界普遍更承认Neural Networks（神经网络）的概念，认为深度学习不过是将Neural Networks换了个名字炒概念，不过这不是重点，
现在深度学习、神经网络两个概念基本上是互通的，一般我们将所有只要和神经网络搭边的模型都合称为人工神经网络（Artificial Neural Networks，ANN）。<br>
## 举个栗子？<br>
和传统机器学习一样，深度学习也分有监督和无监督（当然还有半监督，这个先不说），有监督包括大名鼎鼎的卷积神经网络（Convolutional neural networks，CNN）、循环神经网络（Recurrent neural Network，RNN）等，无监督的有生成式对抗网络（Generative Adversarial Networks，GAN）以及上文提到过的DBN等。<br>
## 业界榜样有哪些？<br>
DL界主要有三驾马车，分别是Geoffrey Hinton、Yann LeCun和Yoshua Bengio，这三位可谓业界泰斗。Geoffrey Hinton号称深度学习之父，BP算法就是这货提出的；Yann LeCun是前者的博士后，提出了CNN，开发了许多著名模型，巨牛逼；Yoshua Bengio年纪最小，但最务实，做了很实验，为理论提供了支持，还开发了著名框架theano。另外欧洲、加拿大也有好几个大神，大家可以去搜一搜。<br>
吴恩达怎么说呢，嗯···不提也罢。
## 我想入坑<br>
个人觉得DL是入门容易精通难【本人也尚处于入门状态···】。推荐书籍有两本，一本是Michael Nielsen所著的《神经网络与深度学习》，主要讲了基础概念和运作原理，并结合minst手写数字库介绍了CNN，浅显易懂，强烈推荐！不过需要一定的python基础，中文版会上传到仓库中；另一本是号称“圣书”的《Deep Learning》，外号“花书”（因为封面上印满了花，实验室好像也有这本书），由两位大神Yoshua Bengio和Aaron Courville所著，需要较好的数学基础，比较难懂···不是很推荐···，部分中文版也会上传到仓库中。<br>
## 实用工具<br>
现在做神经网络肯定不能是自己从0开始写模型的代码了，一般都用别人写好的深度学习框架。最早的是由Yoshua Bengio开发的theano，很好用，引领了一代潮流，后面的框架基本都沿用了theano的思想，不过现在已经不维护了···现在常用的有谷歌的TensorFlow和脸书的caffe、pytorch，微软的CNTK尚不成熟，keras简单好用斗胆推荐给大家。假如想学习的话建议只专注于一个框架，现在服务器的1/2/4号机已经都装了TensorFlow和keras，大家可以去玩玩。
