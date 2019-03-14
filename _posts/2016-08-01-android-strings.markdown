---
author: javen
comments: true
date: 2016-08-01 09:06:32+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/android-strings/
slug: android-strings
title: "\n\t\t\t\t不可不知的 Android strings.xml 那些事\t\t"
wordpress_id: 1110
categories:
- 技术文章
---


				 





<blockquote>

> 
> 编者按：本文为极光工程师 [hevin](https://www.zhihu.com/people/hev1n) 总结的 Android 开发经验。
> 
> 
</blockquote>




相信 strings.xml 已经是大家在 Android 开发中最熟悉的文件之一了，但其实它也有很多需要注意的地方和一些小技巧，知道了这些可以让你的 Android 应用更加规范易用，大家来看看吧。: )





# 不要复用




这一条可能很多人会有不同的意见，因为广为流行的编程理念就在教导我们要复用代码，当然代码复用是很好的理念，可以让程序更加简洁。但也容易形成什么都想复用的思维惯性，而这在某些场景下就可能会造成麻烦。




比如，想象下你在应用中的登录和注册界面中都是用了相同的字符串 - R.string.loading.




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><string name="loading">Loading...</string>
    </code>




之后如果产品需求变更，要分别使用不同的提示语，那就不得不新创建两个 string 并在代码中配置它们。所以如果从一开始就为不同页面分别配置，那需要做的就仅仅是修改下 strings.xml 文件。




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><string name="sign_in_loading">正在登录...</string>
    <string name="sign_up_loading">正在注册...</string>
    </code>




更容易忽略的一个原因是，如果你们的应用不想只是面对国内用户，还要面对其他语系的用户的话，有些语言可能会造成意想不到的问题。




因为一些语言同样一个意思在不同的语境下，会用到不同的单词，个中滋味经历过的自然会懂。





# 良好的文件结构



    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><!-- register start -->
    <string name="register_username">用户名</string>
    <string name="register_password">密码</string>
    <!-- register end -->
    
    <!-- login start -->
    <string name="login_username">用户名</string>
    <string name="login_password">密码</string>
    <!-- login end -->
    </code>




千万不要对此觉得麻烦，使用这样的方式来组织 strings.xml 文件，在应用变得复杂后，还能够很简单的通过搜索甚至观察的方式来找出你想要修改的字符串。





# 格式化




千万不要使用字符串拼接的方法，因为不同语言的句子顺序是多样的，字符串拼接会让你的逻辑非常复杂。




这个时候就可以考虑使用字符串格式化了：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><string name="welcome_messages">Hello, %1$s! You have %2$d new messages.</string>
    </code>




%1$s 代表了会格式化的字符串，%2$d 代表了会格式化的数值并在第二的位置，大家可以以此类推。




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;">// Java 代码：
    Resources res = getResources();
    String text = String.format(res.getString(R.string.welcome_messages), username, mailCount);  // 注意参数的顺序。
    </code>




# 复数名词




**不要**像下面这样在你的 Java 代码中处理单词复数问题，因为不同的语言针对复数会有不同的语法规则。




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><!-- strings.xml start -->
    <string name="book">book</string>
    <string name="books">books</string>
    <!-- strings.xml end -->
    
    if (bookCount == 0) {
        text = getString(R.string.book);
    } else {
        text = getString(R.string.books);
    }
    </code>




**正确**的做法应该是使用 getQuantityString(int id, int quantity) 方法。




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><plurals name="book">
        <item name="one">book</item>
        <item name="others">books</item>
    </plurals>
    
    int bookCount = 4;
    Resources res = getResources();
    String bookCount = res.getQuantityString(R.plurals.book, bookCount);
    // result: books.
    </code>




当然 Quantity String 不仅支持 one，还支持 zero, two, few, many 和 other。
可以自由决定各自情况下，使用什么词语（当然，在中文环境下可能用处并不大）。




并且 Quantity String 也能和上面提到的格式化搭配使用:




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><plurals name="book">
        <item name="one">%d book found.</item>
        <item name="others">%d books found.</item>
    </plurals>
    
    int count = 4;
    Resources res = getResources();
    String bookCount = res.getQuantityString(R.plurals.book, count, count);
    // result: 4 books found.
    </code>




<blockquote>

> 
> 谷歌官方建议，作为一个开发者至少应该给名词提供 ‘one’ 和 ‘other’ 属性。
> 
> 
</blockquote>




# 文本高亮




大家可能知道用 ForegroundColorSpan 或 SpannableStringBuilder 能够高亮一段文本中的某些内容，但这对于多语言应用来说可能并不是最好的方式，因为这两个方法都要依靠待高亮文字的具体位置作为参数。如果应用需要支持的语言多了之后，这就会写大量的 Java 代码，和频繁计算待高亮内容位置。




这个时候就可以试试用 HTML：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block !important; background-color: #f8f8f8; overflow: auto;"><string name="html_text" formatted="false">
    <![CDATA[        
    <font color=\'#28b5f5\'>Hello</font> world.
    ]]>
    </string>
    
    TextView tv = (TextView) findViewById(<img src="file:///C:\Users\ray\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png" alt="" data-mce-src="file:///C:\Users\ray\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png">R.id.tv_txt);
    tv.setText(Html.fromHtml(getString(R.string.html_text)));
    </code>




这其实也是格式化的一种，我们不仅能定义颜色，还可以使用 < b >、< i >、< u > 来分别使字符串具有**加粗**、_斜体_和_下划线_。




其实 strings.xml 的用法远没有很多人想的那么简单，这里也算是抛砖迎玉一下，如果大家想要了解更详细的用法，可以参考[官方文档](img src="file:///C:\Users\ray\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png" alt="" data-mce-src="file:///C:\Users\ray\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png">https://developer.android.com/guide/topics/resources/string-resource.html#StylingWithSpannables)。: )





​







		
