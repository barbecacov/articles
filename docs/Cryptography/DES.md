> 用心分享，共同成长
>
> 没有什么比你每天进步一点点更实在了

<p align="center">本文已经收录至我的GitHub,欢迎大家踊跃star 和 issues。</p>
<p ><h5 align="center"><a  href="https://github.com/midou-tech/articles" target="_blank">https://github.com/midou-tech/articles</a></h5></p>

&emsp;今天要说点和你的隐私有关的事情，在这个信息化的时代，是不是真的有人一手握着你的信息，一手数着钞票呢？答案一定是***当然***。

&emsp;说到网络安全，我就不得不提一个东西——<font face="宋体" color=blue size=4>**密码学**</font>（是不是听起来头皮发麻心跳加速），稳住稳住，老司机上线带你学。今天给大家say&see什么是密码学中的<font face="宋体" color=orange size=4>**DES**</font>。

#### 简介

&emsp;**DES**：

- English Name：Data Encrytion Standard

- 中文名：DES 算法，数据加密标准

  是迄今为止世界上最为广泛使用和流行的一种分组密码算法。（听到这里有没有感觉有点东西）

##### 1、参数介绍：

- data（加解密的数据）：64bit的明文或者密文需要被加密或被解密的数据

- key（加解密的密钥）：8Byte，64bit密钥（56bit密钥+8bit奇偶校验位）

- mode（工作模式）：加密或者解密的工作流程

##### 2、工作流程：

![WechatIMG112](https://tva1.sinaimg.cn/large/006tNbRwly1gaod7ysxdcj30t20y80t6.jpg)

##### 3、初始IP置换：

&emsp;DES算法使用64位的密钥key将64位的明文输入块变为64位的密文输出块，并把输出块分为L0、R0两部分，每部分均为32位。左部分Li=Ri-1,右部分Ri=Li-1⊕F(Ri-1,Ki)<font face="宋体" color=orange size=4>**（注：这里的⊕指二元域上的加法，即异或）**</font>，初始置换规则如下：

```html
1  58,50,42,34,26,18,10,02,
2  60,52,44,36,28,20,12,04,
3  62,54,46,38,30,22,14,06,
4  64,56,48,40,32,24,16,08,
5  57,49,41,33,25,17,09,01,
6  59,51,43,35,27,19,11,03,
7  61,53,45,37,29,21,13,05,
8  63,55,47,39,31,23,15,07,
```

&emsp;即将输入的64位明文的第1位置换到第40位，第2位置换到第8位，第3位置换到第48位。以此类推，最后一位是原来的第7位。置换规则是规定的。L0是置换后的数据的前32位（D1-D32），R0是置换后的数据的后32位（D33-D64）。

##### 4、轮结构：

&emsp;F函数是DES的加密核心，作用也是非常大的，而Feistel结构决定了其加密解密流程是相同的，无论是硬件实现还是软件实现都只需要一种结构，不需要分别实现。

&emsp;**函数F由四步运算构成**：密钥置换；扩展E变换；S-盒代替；P-盒置换。

###### (1)、密钥置换------子密钥生成

&emsp;DES算法由64位密钥产生16轮的48位子密钥。在每一轮的迭代过程中，使用不同的子密钥。

- a、把密钥的奇偶校验位忽略不参与计算，即每个字节的第8位，将64位密钥降至56位，然后根据选择置换PC-1将这56位分成两块C0(28位)和D0(28位)；

- b、将C0和D0进行循环左移变化<font face="宋体" color=orange size=4>**(注：每轮循环左移的位数由轮数决定)**</font>，变换后生成C1和D1，然后C1和D1合并，并通过选择置换PC-2生成子密钥K1(48位)；

- c、C1和D1在次经过循环左移变换，生成C2和D2，然后C2和D2合并，通过选择置换PC-2生成密钥K2(48位)；

- d、以此类推，得到K16(48位)。但是最后一轮的左右两部分不交换，而是直接合并在一起R16L16，作为逆置换的输入块。其中循环左移的位数一共是循环左移16次，其中第一次、第二次、第九次、第十六次是循环左移一位，其他都是左移两位。

###### (2）、扩展E变换

扩展E变换是将数据的右半部分Ri从32位扩展到48位。

扩展置换的目的：

- a、产生与密钥相同长度的数据以进行异或运算，R0是32位，子密钥是48位，所以R0要先进行扩展置换之后与子密钥进行异或运算；

- b、提供更长的结果，使得在替代运算时能够进行压缩。

扩展置换E规则如下：
```html
1  32,01,02,03,04,05,
2  04,05,06,07,08,09,
3  08,09,10,11,12,13,
4  12,13,14,15,16,17,
5  16,17,18,19,20,21,
6  20,21,22,23,24,25,
7  24,25,26,27,28,29,
8  28,29,30,31,32,31,
```

&emsp;也就是说，表中的第i个数据j表示输出的第i位为输入的第j位。例如；输出的第1位是输入的第32位，输出的第7位为输入的第4位。

###### （3）、S-盒

&emsp;S-盒作为feistel结构的<font face="宋体" color=orange size=4>**核心**</font>，起着至关重要的作用。输入的32bit数据经扩展置换之后与48bit子密钥Kn异或，生成的结果作为输入，传入S盒中进行代替运算。S-盒功能是把48位数据变为32位数据，feistel结构中是由8个不同的S盒共同协作完成。其中，每个S-盒有6位输入，4位输出。所以48位的输入块被分成8个6位的分组，每一个分组对应一个S-盒代替操作。经过S-盒代替，形成8个4位分组结果。

<font face="宋体" color=orange size=4>**注：每一个S-盒的输入数据是6位，输出数据是4位，但是每个S-盒自身是64位！！**</font>

&emsp;S-盒1：

```html
14,4,13,1,2,15,11,8,3,10,6,12,5,9,0,7,
0,15,7,4,14,2,13,1,10,6,12,11,9,5,3,8,
4,1,14,8,13,6,2,11,15,12,9,7,3,10,5,0,
15,12,8,2,4,9,1,7,5,11,3,14,10,0,6,13,
```

&emsp;S-盒2： 

```html
15,1,8,14,6,11,3,4,9,7,2,13,12,0,5,10,
3,13,4,7,15,2,8,14,12,0,1,10,6,9,11,5,
0,14,7,11,10,4,13,1,5,8,12,6,9,3,2,15,
13,8,10,1,3,15,4,2,11,6,7,12,0,5,14,9,
```

&emsp;S-盒3：
```html
10,0,9,14,6,3,15,5,1,13,12,7,11,4,2,8,
13,7,0,9,3,4,6,10,2,8,5,14,12,11,15,1,
13,6,4,9,8,15,3,0,11,1,2,12,5,10,14,7,
1,10,13,0,6,9,8,7,4,15,14,3,11,5,2,12,
```

&emsp;S-盒4：
```html
7,13,14,3,0,6,9,10,1,2,8,5,11,12,4,15,
13,8,11,5,6,15,0,3,4,7,2,12,1,10,14,9,
10,6,9,0,12,11,7,13,15,1,3,14,5,2,8,4,
3,15,0,6,10,1,13,8,9,4,5,11,12,7,2,14,
```

&emsp;S-盒5：
```html
2,12,4,1,7,10,11,6,8,5,3,15,13,0,14,9,
14,11,2,12,4,7,13,1,5,0,15,10,3,9,8,6,
4,2,1,11,10,13,7,8,15,9,12,5,6,3,0,14,
11,8,12,7,1,14,2,13,6,15,0,9,10,4,5,3,
```

&emsp;S-盒6： 
```html
12,1,10,15,9,2,6,8,0,13,3,4,14,7,5,11,
10,15,4,2,7,12,9,5,6,1,13,14,0,11,3,8,
9,14,15,5,2,8,12,3,7,0,4,10,1,13,11,6,
4,3,2,12,9,5,15,10,11,14,1,7,6,0,8,13,
```

&emsp;S-盒7：
```html
4,11,2,14,15,0,8,13,3,12,9,7,5,10,6,1,
13,0,11,7,4,9,1,10,14,3,5,12,2,15,8,6,
1,4,11,13,12,3,7,14,10,15,6,8,0,5,9,2,
6,11,13,8,1,4,10,7,9,5,0,15,14,2,3,12,
```

&emsp;S-盒8：
```html
13,2,8,4,6,15,11,1,10,9,3,14,5,0,12,7,
1,15,13,8,10,3,7,4,12,5,6,11,0,14,9,2,
7,11,4,1,9,12,14,2,0,6,10,13,15,3,5,8,
2,1,14,7,4,10,8,13,15,12,9,0,3,5,6,11,
```
S盒的计算：第一位和第六位确定行数，第二、三、四行确定列数，在表中找出对应的数据，将其表示为二进制数据。

###### （4）、P-盒

&emsp;在S-盒代替运算中，每一个S-盒得到4位，8盒共得到32bit输出数据。这32位输出作为P盒置换的输入块，最终输出为32bit的输出数据。P盒置换将每一位输入位映射到输出位。任何一位都不能被映射两次，也不能被略去。

##### 5、逆置换：

&emsp;将初始置换进行16次的迭代，即进行16层的加密变换，这个运算过程我们暂时称为函数f。得到L16和R16，将此作为输入块，进行逆置换得到最终的密文输出块。逆置换是初始置换的逆运算。从初始置换规则中可以看到，原始数据的第1位置换到了第40位，第2位置换到了第8位。则逆置换就是将第40位置换到第1位，第8位置换到第2位。以此类推，逆置换规则如下：

```html
1  40,08,48,16,56,24,64,32,39,07,47,15,55,23,63,31,
2  38,06,46,14,54,22,62,30,37,05,45,13,53,21,61,29,
3  36,04,44,12,52,20,60,28,35,03,43,11,51,19,59,27,
4  34,02,42,10,50,18,58 26,33,01,41,09,49,17,57,25,
```

&emsp;**注**：DES算法的加密密钥是根据用户输入的秘钥生成的,该算法把64位密码中的第8位、第16位、第24位、第32位、第40位、第48位、第56位、第64位作为奇偶校验位,在计算密钥时要忽略这8位.所以实际中使用的秘钥有效位是56位。

&emsp;秘钥共64位，每次置换都不考虑每字节的第8个比特位，这一位是在密钥产生过程中生成的奇偶校验位，所以64位秘钥的第8、16、24、32、40、48、56、64位在计算秘钥时均忽略。

##### 6、解密过程：

&emsp;DES的解密过程和DES的加密过程基本类似，唯一的不同是将16轮的子密钥序列的顺序反过来，即在加密过程中所用的加密密钥k1，...，k16在解密过程中需要转换成k16，...，k1。

##### 7、DES算法整理：

- 分组加密算法：以64bit为分组。输入64bit明文，输出64bit密文。
- 对称算法：加密和解密使用同一秘钥。
- 有效秘钥长度：56bit秘钥，通常表示为64位数，但每个第8位用作奇偶校验，可以忽略。
- 代替和置换：DES算法是两种加密技术的组合：混乱和扩散。
- 易于实现：DES算法只是使用了标准的算术和逻辑运算，其作用的数最多也只有64 位，因此用70年代末期的硬件技术很容易实现算法的重复特性使得它可以非常理想地用在一个专用芯片中。

### 龙跃十二的热门文章：

[🔥🔥学习linux命令，看这篇2w多字的命令详解就够了](https://mp.weixin.qq.com/s/nMTsD2y_drV971RxdE1a5g) 

[🔥HTTP就是这么简单](https://mp.weixin.qq.com/s/V0_fcjr9t-lyqy2qyg6MWw)

[🔥Redis基础](https://mp.weixin.qq.com/s/OZRGLGk3MkdkwaNliITTUQ)

[🔥顶级程序员工具集](https://mp.weixin.qq.com/s/Sel5ySSh0YJXdQ6KExQxyw)

[🔥二十多个免费高清图片素材网站送给你](https://mp.weixin.qq.com/s/pcMWdKh9cFMS75sAxgCrWg)

[🔥什么是消息队列？](https://mp.weixin.qq.com/s/NuHIVl5SjQ0_v1EPKnT9Vw)

[🔥初学者该如何选择一门编程语言？](https://mp.weixin.qq.com/s/wTv67aYUHmrNGPWsWNbpBg)



`如果有人想发文章，我这里提供`<font face="宋体" color=blue size=4>**有偿征文**</font>`(具体细则微信联系)，欢迎投稿或推荐你的项目。提供以下几种投稿方式：`

- `去我的github提交 issue:` https://github.com/midou-tech/articles

- `发送到邮箱: 2507367760@qq.com 或者 longyueshier@163.com  或者 longyueshier@gmail.com`

- `微信发送: 扫描下面二维码，公众号里面有作者微信号。`

`精选文章都同步在公众号里面，公众号看起会更方便，随时随地想看就看。微信搜索 龙跃十二 或者扫码即可订阅。`

<p align="center"><image src="https://tva1.sinaimg.cn/large/006tNbRwly1galsp9a07kj30p00dwae3.jpg" ></image></p>
> 我喜欢那些闪光的东西，比如冬日的雪花，天上的星星，还有你发光的眼睛。