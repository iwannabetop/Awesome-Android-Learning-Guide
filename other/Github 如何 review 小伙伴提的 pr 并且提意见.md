# 文章出处
 
 > 文章出自：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)
 
 > 作者：[shixinzhang](http://blog.csdn.net/u011240877)

# Github 如何 review 小伙伴提的 pr 并且提意见

主要这么几步：

1. 打开要审核的 pull request
2. 点击第三个小标题 “Files changed”（可以看到这个提交里改变的文件）
3. 找到 md 文件，如果没加载出来只显示 “Load diff”，就点一下
4. 出现类似 git 对比的页面，右面是这次提交的内容
5. 鼠标放在某行，会出现加号，点击一下出现输入框，写下意见



接下来截图示范一下。

1.首先进入某个 pr

![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbh98z81j212o0ca0v6.jpg)

Conversation 不为 0，说明可能有别人提意见了，先看看别人怎么说。

Files changed 表示这次提交修改了几个文件。

2.查看修改的文件

重点看 md 文件，如果内容太多没有直接加载出来，就点击这个 Load.

![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbhstbtaj21jq0ait9v.jpg)

3.看到哪里就有问题就在对应的地方留言

> 留言次数没有限制。

![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbmebmzpj20ua0ken2j.jpg)

上面的图有点乱，如果你想最后一起发送意见，就点击 ``Start a review``，点击后会这样：

![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbikugofj20t60ci76h.jpg)

4.提交审核意见

如果选择最后一起发送意见，别忘了到 ``Conversation`` 里提交意见。

![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbmu00udj21600pogq0.jpg)

5.提交后 Conversation 的数字就会改变


![](http://ww1.sinaimg.cn/large/b1aad299gy1fktbrlus59j20oo02yaa9.jpg)

完成！