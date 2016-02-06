---
layout: post
title:  "HTML5 audio API"
date:   2013-11-28 
categories: projs
---



小光棍节还在写这个 真是#注定孤独一生#

FYP是折腾和HTML5 audio有关系的东西 今天本来想要翻译下audio API为了找一个方法的 但是后来想想 那个方法逻辑很简单 不如自己写 然后也就自己写了

但是总要记录下来使用心得吧

今天遇到的问题是 总是无法获取audio对象 明明使用了`$('audio')`了 但是依然无法控制audio的属性

其实问题是在于 获取audio对象的时候不能用`$('audio')`而是应该使用`document.getElementById(id)`今天稍微搜了一下 没有找到原因 但是我记得有人解释过的！所以明天接着找一下~

update 2013/11/15



今天简直是##￥%￥#%#￥ 静态的页面没有问题 和后台连起来了以后突然进度条就不能拉动了… 我都解释无能了 对着一个静态一个动态页面 相同的js不同的状态都要跪下啦 最重要的是完全不明白为什么动态的就是不对

非常简单的进度条拖动问题 两行代码搞定的那种

``` 
$processYet.width(toWhere);
GLOBAL.audio.currentTime = percentage*GLOBAL.audio.duration;
```



后来发生了最无语的事情… 我先是将`GLOBAL.audio`换成了`document.getElementById('player');`就行了！！

然后检查了一遍 其实在页面加载好的时候 我已经做了`GLOBAL.audio = document.getElementById('player');`了 所以我就把代码还回来 还是用 `GLOBAL.audio` 然后尼玛就可以拖动进度条了 简直要咆哮了我

写audio的时候发现一位[前辈](http://www.feelcss.com/)

update 2013/11/23



相同的问题再次出现了，所以先把老的办法试了一下，即替换成`document·getElementById('player');`的方式，但是这次奇迹没有发生，而且回头看前段时间做的player.html页面，再次不可以拖动了。

后来在[这里](http://webcache.googleusercontent.com/search?q=cache:YTjbk6P8kcsJ:pervasivecode.blogspot.com/2012/09/currenttime-not-working-with-html5.html+&cd=7&hl=zh-CN&ct=clnk&gl=cn)发现了问题所在。无法拖动的根本原因在无法改变audio的currentTime，要改变currentTime需要后台允许一个叫做[Http Range Requests](http://www.web-polygraph.org/docs/userman/ranges.html)在stack overflow上有人教了怎么分辨server是不是支持Http Range Requests，详细请看[这里](http://stackoverflow.com/questions/720419/how-can-i-find-out-whether-a-server-supports-the-range-header)简单来说就是，先把GET方法改成HEAD请求，然后如果是支持Http Range Requests的server，请求页面后返回的是206，否则就是返回正常的200。

但是现在即使是后台支持了Http Range Requests，有些歌曲还是不能够拖动，但是有些又都可以，格式也都是mp3的。这个问题还要再看看啊，也许JS也应该做些什么？

update 2013/11/28



难道一个`audio.src`是不可以赋值给另一额`audio.src`的么？这个问题纠结了我一个下午依然没有得到解决，我把问题放到了 [stackover flow](http://stackoverflow.com/questions/20244540/cannot-trigger-durationchange-of-html5-audio)上了，也写了个小例子放在[jsFilddle](http://jsfiddle.net/DTAve/9/)上

可以确定的是问题出现在赋值语句上，而且是在server上运行才会出现这样的问题，在本地是不会的。不仅仅是赋值，即使是将两个`audio`指向同一个mp3地址都会出问题，在jsFilddle上的例子是最原始的`audio`是好的，在对新的audio操作之后，原始的audio依然可以用，而复制的两个audio是不可以用。

而在我的的project中的问题是，旧的audio是不可以听了，但是新的是可以的。所以和我模拟出来的bug场景是相反的。

这个问题已经被解决的 感谢万能的stackover flow 外国友人太赞了

主要的原因是 浏览器不允许同时操作同一个mp3文件 所以第二个src要使用的音乐文件后面需要跟上参数`?....`就是类似于使用GET方法时 URL上面带着的参数 但是我觉得的奇怪的是 这样为什么就不算操作同一个文件了呢？直到现在我也没有找到答案 知其然不知其所以然 不知道这个答案要找到上面时候唉

update 2013/12/5



blob文件转成wav是要在客户端来转还是在服务器端来转呢？这是我们最近遇到的问题 从常理上讲 绝对不应该发送wav给服务器 也就是说blob不应该在客户端转成wav发送给服务器 这件事情应该要服务器来做 可是由于技术上的问题 前几天我们没有找到合适的方式在服务器端转 一开始我是直接将录音后的blob文件地址传给后台 这么做的话 这个blob文件地址其实指向的是用户的浏览器的缓存区 后台似乎无法根据这个缓存区地址拿到blob原文件 那么前台能做的就是要直接把blob传给后台

一开始我是不能接受这样的方式啦 觉得很奇怪 为什么不能通过地址访问呢？后来发现stackover flow上有好几个问题都是问blob转wav的 而且也都是在使用“录音”这样的功能的时候（其实也都是WebRTC啦）谢谢@高灰 一开始就直接发给我了[这个](undefined)直接告诉了我解决方式了

现在要做的就是把blob读出来 传给后台 需要用到`FormData()`这个obj来模拟一次表单提交 后台用接收表单的方式就能得到blob的值的 这个方法在网上有很多教程 是FF先开始用的 后来发展的主流浏览器都接受了 其实还是挺新的东西 司徒正美前辈的blog上也有一些很简单的用法

但是在参照stackover flow上的ajax方法时 又出现了另一个问题：我传入的blob和真正录音生成的blob文件是不同的（好的这里我又不知道是为什么了） 所以我就用了非常简单粗暴的方式解决这个问题：当生成录音blob的时候 直接将这个blob放入FormData()中传入后台

