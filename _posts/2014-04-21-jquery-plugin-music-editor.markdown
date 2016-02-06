---
layout: post
title:  "jquery-plugin-music-editor"
date:   2014-04-21
categories: projs
---



和[HTML5 audio API使用心得](/html5-audio.html)相似，现在我正在进行Final Year Project II，准备将上个学期的Music网站中剪辑器的部分抽出来，作为一个jQ的插件，在完成以后也会发布到[jQ Plugin Registry](https://plugins.jquery.com/)上。

理论性的笔记或者看到别人博客里面的一些经验准备记在[印象笔记](http://app.yinxiang.com/shard/s20/sh/5aa34aeb-87a6-49a6-b801-6b906eb041d9/60a679495bb3a79c5e24911918ce44c6)上。而自己写代码时候的感悟或者积累出来的经验就放在这篇post中。

大致介绍一下现在的Music Editor已有的功能：

1. *1.*对给定列表中歌曲进行剪辑。点击列表中的歌曲进行播放。![musicEditor](https://c1.staticflickr.com/3/2917/13936421172_d889a00f66_c.jpg)
2. *2.*在播放的同时，需要用户设置该歌曲需要剪辑的片段，所以需要设置歌曲的剪辑起始点和终止点。会根据这两个时间点来获取这首歌用户想要的区间，不过现在只能剪辑一个区间出来，因为剪辑点只能设置**exactly**两个，![controller](https://c1.staticflickr.com/3/2938/13936425251_afb17f2282_z.jpg)
3. *3.*用户可以尝试将剪辑好的歌曲按照剪辑的先后顺序试听一遍。![editorBuffer](https://c2.staticflickr.com/6/5136/13960255623_cff09002c9.jpg)
4. *4.*播放歌曲时显示歌词。![lyrics](https://c1.staticflickr.com/3/2917/13936421172_d889a00f66_c.jpg)

具体的实现可以看做是一个player+多个临时生成的<audio>，当试听结束时这些临时的<audio>将会被移除。这些临时的当这个剪辑器转成插件时，我想要这么做：

1. *1.*本身的剪辑器有一套默认的CSS。提供了最基本的player、歌词还有缓存区（即上图中，剪辑完毕以后出现在页面偏下位置的一段矩形）。
2. *2.*player是一个带有特定id的没有controls属性的<audio>标签，id可以由用户定义。同时还能配置是设置剪辑点的icon/别的什么东西（default是一把剪刀，样式可CSS使用者来覆盖）。
3. *3.*允许删除剪辑出来的片段，或者更换片段的位置。这些也只会是单独的方法，可以绑定到任何DOM元素上。
4. *4.*要在页面上展现音频的信息，那么一定就要有音频的数据的传递。所以音频的信息使用JSON来存放，JSON的格式是我已经定好，使用者需要遵循这个格式。

现在就是无插件版的还有很多bug，我已经没有考虑兼容问题了，但是CSS和JS依然bug多多，甚至还有没有实现的功能：

1. *1.*无法对同一首歌进行多次剪辑，这个涉及到HTML5 Audio API中的`currentTime`属性，在前面的post中提到过我是用控制音频的currentTime来实现只播放某个音频的特定区间的内容，这个是用来实现‘‘试听’’的。后来发现如果一个音频的currentTime被多次操作，就会出现各种神奇的问题。
2. *2.*对于歌曲片段的操作还没有实现：就是更换片段的顺序，删除片段……
3. *3.*缓存区中的歌曲是会显示歌曲名字的，如果歌曲名字太长，样式就会乱掉

update 2014/05/03

做歌词滚动的时候发现之前写的一直是有问题的，从前我用`overflow: auto`和固定高度来定出一块歌词区域，像这样![originLRC](https://c2.staticflickr.com/8/7433/14095937305_3667fbcca7_n.jpg)。效果就是出现局部的滚动条，好啦我知道滚动条的样式好丑。

按照lrc文件和currentTime做同步的时候，我是用`top`来控制整个lrc显示区域的移动的，之前一直没有发现歌词区域移动的情况不对：右侧滚动条不会滚动，滚动的文本，造成的结果就是，用户没法往上拉动滚动条，看不了已经唱过的歌词。

这么个蛋疼的问题我居然今天才发现= =|||，抓耳挠腮了半个小时，用jQuery的`animate`和`scrollTop`实现了移动的是滚动条而不是文本。

update 2014/06/30

没有想到这一次更新这篇blog的时候我已经毕业了，祝我毕业快乐，下一站就是预期中的HKUST。这次更新这篇blog是因为我已经把这孩子发布到[jQuery Plugin Repo](http://plugins.jquery.com/easyAuditor/)上了，我也很确定这个插件目前还是做得够烂的- -，毫无兼容性可言，我觉得未来一年里面我还会是不是回过头来看看她。这次的更新就说一说发布插件到[jQuery Plugin](http://plugins.jquery.com/)上的问题吧，在发布的时候遇到了一点小问题，但是最近Google一直无法搜索，百度也搜不到解决的办法，所以一直自己在找到底是哪里除了问题，后来解决的时候觉得自己太蠢了，所以现在只写发布插件这一块，希望如果之后有人遇到相同的问题，还能找到这里地方。跟着[jQuery Plugin里面的发布步骤一步步来吧，一共3步，所以不要害怕。](http://plugins.jquery.com/docs/publish/)

1.  首先  ，你的代码已经在github上了吗？如果还没有放到github上的，先把它们放上去吧当代码在你的某一个repo中的时候，进入这个repo，你会看到和下面这张图片很相似的页面对吧？注意一下页面上红框中的内容。![publish jQuery Plugin-s1-0](https://c2.staticflickr.com/4/3867/14345437498_07e1510338_z.jpg)你要做的第一件事就是**点击右侧的Settings**，去添加jQuery Plugin service hook到你的插件repo中，添加了这个hook就是使得你的插件repo可以和jQuery plugin repo联系起来。点击Settings，页面跳转后你会看到左侧有几个button可以点击，点击第三个'Webhooks & Services'，而后应该要看到![publish jQuery Plugin-s1-1](https://c2.staticflickr.com/4/3870/14532006915_9e96ddd2ba_c.jpg)，还是请注意红框，现在要添加的就是jQuery Plugin service，然后激活它。遵照这两个张图的做法就可以了，一般这一步不会有什么问题的：![publish jQuery Plugin-s1-2](https://c1.staticflickr.com/3/2909/14345402249_7f0cf7b7b6_c.jpg)![publish jQuery Plugin-s1-3](https://c1.staticflickr.com/3/2927/14508897236_7075f4e61b_z.jpg)
2. 第二步就是要写一个Manifest，还记得第一张图其中一个红框里有个xxxx.jquery.json吗？那个就是你的plugin的Manifest文件，Manifest是记录插件基本信息的文件，jQuery对这个文件是有[要求](http://plugins.jquery.com/docs/package-manifest/)的，一定要看一下Manifest的具体要求，有些值是必须的。这张是我的Manifest（还是注意红框中的内容哦）：![publish jQuery Plugin-s2-0](https://c1.staticflickr.com/3/2901/14528762041_624f3a1813.jpg)写完Manifest以后还要去[jQuery的Pulish页面](http://plugins.jquery.com/docs/publish/)检测是否是合法的Manifest文件，如果是合法的会看到提示的：![publish jQuery Plugin-s2-1](https://c2.staticflickr.com/4/3863/14531127452_db8059ab17_b.jpg)然后将Manifest推送到github上。**做下面的步骤之前一定要先推送哦！**
3. 终于到了最后一步，非常简单，为你的plugin打上一个tag吧。还记得上文里的Manifest截图中的红框吗？这里要说的就是tag和Manifest的version一定要统一，而且执行完`git push origin --tags`以后，绝对不可以去修改这个tag，删除然后重新`git tag`是不可以的，通常会出错，我3周前就是卡在了这里，好吧…当时我也忘了把Manifest一起push上去= =。