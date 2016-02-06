---
layout: post
title:  "Element without height to be totally center"
date:   2013-10-27 
categories: study
---



参考自[此处](http://www.cnblogs.com/rubylouvre/archive/2010/07/08/1774025.html)以及[此处](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CCoQFjAA&url=http%3a%2f%2fblog%2ejobbole%2ecom%2f46574%2f&ei=p9BvUtjTJMHKkgWzg4DAAw&usg=AFQjCNHXkxJ43RHgaXpTknYzib5Li05jyA&sig2=MLrzpA61T3VNuFKSVOU9ag)实现方法多种，这里只记下三种，一个是传统方法，一个是技巧性高，还有一个是CSS3。



1. 这种是传统方法，未知高度的元素外嵌套两层，同时要定义**body**和**html**的height，这样的目的主要是为了做到垂直居中。如果未知高度的元素是width固定的话，用`margin: 0 auto`就可以水平居中了，非块级的话还要加上`display: inline-block || block`。
   
   ``` css
   body, html
   	height: 100%
   .noHeight-wrapper
       width: 100%
       height: 100%
       overflow: hidden
       position: relative
       display: table
       .noHeight-inner
           display: table-cell
           vertical-align: middle
           .noHeight
               display: block
               position: relative
               top: -50%
               margin: 0 auto
   ```
   
2. 下面这样是传说中的“CSS完美居中”，主要是利用`position：absolute; top: 0; left:0; right: 0; bottome:0`来实现的，但是这个未知高度的元素至少需要定义一个`min-height: 1px；`具体的实现原理请参考文章开头的reference.
   
   ``` scss
   .noHeight
       margin: auto
       min-height: 1px
       position: absolute
       top: 0
       left: 0
       bottom: 0
       right: 0
       overflow: auto
   ```
   
3. CSS3提供了`display: box`现在似乎更加流行的使用flex-box，但是作为box都用不清楚的人...要做到垂直居中一样要`body,html{height: 100%;}`, 这个属性还没有被W3C正式加入(?!)，所以使用的时候还需要加上浏览器的prefix。
   
   ``` scss
    body, html
        height: 100%
      .img-wrapper
          height: 100%
          display: -webkit-box
          -webkit-box-orient: horizontal
            -webkit-box-pack: center
           -webkit-box-align: center
          display: box
          box-orient: horizontal
          box-pack: center
          box-align: center
   ```