## project1
因为要做实验更新了Visual Studio，过程中也遇到了一些小问题，是一些设置，乱码之类的问题，但都通过上网一一解决了，总的来说还算顺利。

## project2
因为第一次实验已经打好了基础，本次实验基本上没有碰到什么瓶颈，就是刚开始自己写的代码太过繁琐，我在后面将代码做了优化。

## 简单抠图
一开始我想把超人直接抠到space的原图中去，但是尝试了好久最终以失败告终，于是我新建了图像，在内存中把space的像素值改成了Superman中超人部分的像素值，本来我以为只要三个通道的像素值都满足条件即可，所以一个通道一个通道的进行修改，后来发现这种方法不对，必须让三个通道的像素值同时满足条件才可以，如此一来便成功了。感觉这个实验还蛮好玩的。

## 线性滤波
这个实验做了好久，但是边缘检测，锐化还有浮雕的图像一直出错，后来做图像融合的时候，我发现数据类型都是浮点型，于是我把这个实验的数据类型也改了，结果就对了，在浮雕那一块，刚开始我不知道怎么将结果偏移来得到灰度图像，也是纠结了很久，不过最终得到了正确结果还是很开心的。

## IHS遥感图像融合
这次实验没有遇到什么大问题，主要是不了解直方图匹配，但是实验中没有用到。
