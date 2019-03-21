---
author: javen
comments: true
date: 2016-04-21 08:26:43+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/react_native_android/
slug: react_native_android
title: "\n\t\t\t\tReact Native Android 入门与实践\t\t"
wordpress_id: 833
categories:
- 技术文章
tags:
- android
- React Native
- React
---


				作者：KenChoi

本文旨在指导 React Native 初学者如何使用 React Native 初步构建 Android 应用。

在看本文之前，你应该具备了理解 React Native 的基本概念，以及搭建好了所需的环境（如果没有，参考[官方网站](https://facebook.github.io/react-native/docs/getting-started.html#content)）。接下来，我们一起来实现一个简单的 PushDemoApp，借助 jpush-android-sdk 就可以实现推送功能。**这里是[PushDemoApp的源码](https://github.com/jpush/jpush-react-plugin)**。

我们先来看一下 PushDemoApp 最终的界面效果：


[![react native with jpush](/images/2016/04/1745101-efdbff67863495f3.gif)](/images/2016/04/1745101-efdbff67863495f3.gif)






怎么样，是不是很心动呢，接下来我们来看看如何一步步打造一个React Native应用吧。首先创建一个Project，在命令行中输入


<blockquote>react-native init PushDemo</blockquote>


这样就创建了一个名为 PushDemo 的项目，使用 Android Studio 打开PushDemo/android项目，接下来改造 android 的工程结构以兼容 Eclipse，如图所示：


[![react native with jpush](/images/2016/04/1745101-9aebd7437788b681.jpeg)](/images/2016/04/1745101-9aebd7437788b681.jpeg)






并且在build.gradle加上相关配置，此处不再赘述，可以参考github上的源码。


## 配置入口


React Native应用有Native和JS两个入口：MainActivity以及index.android.js。


#### Native入口


Android的入口是在 MainActivity 中实例化 ReactInstanceManager，并通过ReactRootView启动App。

    
    <code class="scala">mReactInstanceManager = <span class="hljs-type">ReactInstanceManager</span>.builder()          
      .setApplication((<span class="hljs-type">Application</span>) <span class="hljs-type">PushDemoApplication</span>.getContext())        
      .setBundleAssetName(<span class="hljs-string">"index.android.bundle"</span>)        
      .setJSMainModuleName(<span class="hljs-string">"react-native-android/index.android"</span>)        
      .addPackage(<span class="hljs-keyword">new</span> <span class="hljs-type">MainReactPackage</span>())        
      .addPackage(<span class="hljs-keyword">new</span> <span class="hljs-type">CustomReactPackage</span>())        
      .setUseDeveloperSupport(<span class="hljs-type">BuildConfig</span>.<span class="hljs-type">DEBUG</span>)        
      .setInitialLifecycleState(<span class="hljs-type">LifecycleState</span>.<span class="hljs-type">RESUMED</span>)        
      .build();
    mReactRootView.startReactApplication(mReactInstanceManager,<span class="hljs-string">"PushDemoApp"</span>,<span class="hljs-literal">null</span>);</code>


上面的setBundleAssetName("index.android.bundle")，如果没有生成index.android.bundle文件，可以在app目录下新建一个assets文件夹，然后在命令行中启动packager（使用npm start命令即可），启动后再输入以下命令生成bundle文件：

    
    <code class="objectivec">curl <span class="hljs-string">"http://localhost:8081/index.android.bundle?platform=android"</span> -o <span class="hljs-string">"android/app/assets/index.android.bundle"</span></code>


上面-o后面的路径即为存放index.android.bundle的路径，当然你也可以修改这个路径。

setJSMainModuleName("")这一句里面的参数则是index.android.js所在的文件路径，这个路径是相对于package.json的路径，在本例中则把index.android.js放在了react-native-android目录下。


#### JS入口


打开 index.android.js文件（推荐使用Sublime Text开发JS，有丰富的插件支持），可以看到最下面调用了 AppRegistry 来注册JS入口：

    
    <code class="coffeescript">React.AppRegistry.registerComponent(<span class="hljs-string">'PushDemoApp'</span>, <span class="hljs-function"><span class="hljs-params">()</span> =></span> PushDemoApp);</code>


要注意上面函数的第一个参数即为在MainActivity中mReactRootView.startReactApplication()方法中的第二个参数。


## 编写JS


配置好入口后，接下来我们就可以开始编写JS了。我们从index.android.js文件开始讲解相关语法以及布局。首先看到第一句： ‘use strict’;放在第一行表明整个脚本都使用了JS的严格模式，只需要记住开发的时候一般都使用严格模式就行了。

    
    <code class="javascript"><span class="hljs-keyword">import</span> React <span class="hljs-keyword">from</span> <span class="hljs-string">'react-native'</span>;
    <span class="hljs-keyword">import</span> PushActivity <span class="hljs-keyword">from</span> <span class="hljs-string">'./push_activity'</span>;
    <span class="hljs-keyword">import</span> SetActivity <span class="hljs-keyword">from</span> <span class="hljs-string">'./set_activity'</span>;
    <span class="hljs-keyword">import</span> WebActivity <span class="hljs-keyword">from</span> <span class="hljs-string">'./web_activity'</span>;</code>


import from这是ES6的语法，关于React Native的代码规范，推荐[参考这个](https://github.com/airbnb/javascript/tree/master/react)。本例所使用的都是ES6语法。from后面是所需文件的相对路径，和ES5中的require类似，可以[参考这个](http://www.ruanyifeng.com/blog/2015/05/require.html)

    
    <code class="scala"><span class="hljs-keyword">var</span> {  
      <span class="hljs-type">BackAndroid</span>,  
      <span class="hljs-type">Component</span>,  
      <span class="hljs-type">Text</span>,  
      <span class="hljs-type">TextInput</span>,  
      <span class="hljs-type">View</span>,  
      <span class="hljs-type">Navigator</span>,
    } = <span class="hljs-type">React</span>;</code>


上面的代码声明了React的一些组件，下面就可以直接使用了。

    
    <code class="java">constructor(props) {    
      <span class="hljs-keyword">super</span>(props);        
      <span class="hljs-keyword">this</span>.state = {      
        tag: <span class="hljs-string">''</span>, 
        appKey: <span class="hljs-string">'abc'</span>,   
      }  
      <span class="hljs-keyword">this</span>.renderScene = <span class="hljs-keyword">this</span>.renderScene.bind(<span class="hljs-keyword">this</span>);  
    }</code>


这个构造函数可以用来做一些初始化的动作，使用 this.state = {} 替代了ES5中 getInitialState 函数。

**React Native 较为出彩的一点就是将所有的组件都看成了状态机，通过设置组件的状态或者属性就可以触发render函数重新渲染，实现刷新界面的效果。**

    
    <code class="mathematica"><<span class="hljs-keyword">Text</span> style = <span class="hljs-list">{ styles.btnText }</span>
      <span class="hljs-list">{ this.state.appKey }</span>
    </<span class="hljs-keyword">Text</span>></code>


上面Text相当于Android中的TextView控件，Text控件包裹的内容引用了一个状态：appKey，appKey在构造函数中被初始化为abc，那么这个Text就会显示abc这个字符串。使用this.setState方法就可以改变状态，比如：

    
    <code class="css">  <span class="hljs-tag">this</span><span class="hljs-class">.setState</span>(<span class="hljs-rules">{ <span class="hljs-rule"><span class="hljs-attribute">appKey</span>:<span class="hljs-value"> <span class="hljs-string">'123'</span> })</span></span>;</span></code>


这样Text就会刷新，重新显示为123。还可以将状态作为属性传递给其他组件，比如：

    
    <code class="mathematica"><Actionbar style = <span class="hljs-list">{ styles.actionbar }</span>>
        onselect = <span class="hljs-list">{ this.onSelectMenu }</span>
        currentPage = <span class="hljs-list">{ this.state.page }</span>
    </Actionbar></code>


上面的代码将this.onSelectMenu以及this.state.page作为属性传递到Actionbar这个组件了，前者是一个函数，后者是一个状态，这样在Actionbar中就可以通过this.props.onselect以及this.props.currentPage来获得这两个属性。当这两个属性变化时，也会触发render函数重新渲染。在Redux架构中，通过改变属性来刷新界面非常常见，后面会讲到这个架构。

接下来是这一句


<blockquote>this.renderScene = this.renderScene.bind(this);</blockquote>


在ES5下，React.createClass 会把所有的方法都 bind 一遍，这样可以提交到任意的地方作为回调函数，而this不会变化。但官方现在逐步认为这反而是不标准、不易理解的。在 ES6 下，你需要通过bind来绑定this引用，或者使用箭头函数（它会绑定当前scope的this引用）来调用。关于ES5与ES6的写法对照可以[参考这个](http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8/2)。

接下来先看到 componentDidMount() 和 componentWillUnmount() 这两个函数，这两个是组件的生命周期函数，在组件进入生命周期的不同阶段时自动调用。关于组件生命周期的介绍可以[参考这篇博客](http://blog.csdn.net/sbsujjbcy/article/details/49925781)。在componentDidMount()声明了点击Android的返回键时从navigator的栈中弹出一个页面，类似于Android中的onBackPress()方法。

接下来是 render() 函数，这个函数就是绘制界面的地方。React Native可以用Html或JSX来编写界面，推荐使用官方推荐的JSX语法。

    
    <code class="scala">render() {
          <span class="hljs-keyword">return</span> (
              <<span class="hljs-type">Navigator</span>
                  initialRoute = { {name: <span class="hljs-symbol">'pushActivit</span>y' }}
                  configureScene = { <span class="hljs-keyword">this</span>.configureScene }
                  renderScene = { <span class="hljs-keyword">this</span>.renderScene } />
            );
        }</code>


可以看到这里仅仅是返回了一个Navigator。


#### Navigator


React Native使用Navigator来控制页面的跳转。Navigator有3个属性：



	
  * initialRoute 声明了初始要显示的界面，这里是传了一个route name过去即“pushActivity”

	
  * configureScene 指定了页面跳转的动画，查看Navigator.SceneConfigs来获取默认的动画和更多的场景配置选项。

	
  * renderScene 声明了页面该如何跳转

    
    <code class="cs">renderScene(router, navigator) {    
      <span class="hljs-keyword">var</span> Component = <span class="hljs-keyword">null</span>;    
      <span class="hljs-keyword">this</span>.navigator = navigator;    
      <span class="hljs-keyword">switch</span>(router.name) {      
        <span class="hljs-keyword">case</span> <span class="hljs-string">"pushActivity"</span>:        
          Component = PushActivity;        
          <span class="hljs-keyword">break</span>;      
        <span class="hljs-keyword">case</span> <span class="hljs-string">"setActivity"</span>:        
          Component = SetActivity;        
          <span class="hljs-keyword">break</span>;      
        <span class="hljs-keyword">case</span> <span class="hljs-string">"webActivity"</span>:        
          Component = WebActivity;    
      }    
      <span class="hljs-comment">//将navigator作为属性传给其他页面，这样在其他页面中就可以使用this.props.navigator拿到navigator了    </span>
      <span class="hljs-keyword">return</span> <Component navigator = { navigator } />
    }</code>


renderScene()有两个参数，router对象指定要跳转的界面，router.name可以拿到刚才我们在initialRoute时传过来的name，此处仅仅将name传过来，然后根据name来返回Component，后面会看到其他写法。navigator可以作为属性传给其他页面，还可以带一些参数传到其他页面，相当于Android中的Intent。比如：



    
    <code class="cs"><span class="hljs-keyword">return</span> <Component
              navigator = { navigator }
              <span class="hljs-keyword">params</span> = {
                name: <span class="hljs-string">''</span>,
                title: <span class="hljs-string">''</span>,
              }
            /></code>


这样在其他页面可以通过this.props.name以及this.props.title来拿到这两个值，相当于getIntent()。关于页面的启动模式，即相当于Android中Activity的4种启动模式以及flag的设置，在React Native中则是在跳转时调用Navigator的内部方法来控制，如下：



	
  * getCurrentRoutes() - returns the current list of routes, 返回当前栈中的所有路由

	
  * jumpBack() - Jump backward without unmounting the current scene，在保留当前场景的情况下返回到上一个场景

	
  * jumpForward() - Jump forward to the next scene in the route stack 回到刚才jumpBack()之前的场景

	
  * jumpTo(route) - Transition to an existing scene without unmounting 跳转到当前栈中的某个场景并且不会卸载掉之前的场景

	
  * push(route) - Navigate forward to a new scene, squashing any scenes that you could jumpForward to 往栈中push一个新场景

	
  * pop() - Transition back and unmount the current scene 在栈中卸载掉当前场景，并返回上一个场景

	
  * replace(route) - Replace the current scene with a new route 用一个新路由替换掉当前场景

	
  * replaceAtIndex(route, index) - Replace a scene as specified by an index 替换指定索引的路由场景

	
  * replacePrevious(route) - Replace the previous scene 替换掉上一个场景

	
  * resetTo(route) - Navigate to a new scene and reset route stack 跳转到一个新场景，并且重置路由栈，相当于Android中的new Task

	
  * immediatelyResetRouteStack(routeStack) - Reset every scene with an array of routes 用一个新路由栈替换之前的路由栈

	
  * popToRoute(route) - Pop to a particular scene, as specified by its route. All scenes after it will be unmounted 跳转到指定的场景，在这个场景之上的都会被卸载掉。相当于Android中的CLEAR_TOP标志

	
  * popToTop() - Pop to the first scene in the stack, unmounting every other scene 跳转到第一个场景，其他的场景将会被卸载




#### 布局


我们再来看一下push_activity.js这个类，这是应用的启动界面，先来看一下render()方法，render()中return的内容相当于Android中的xml布局，React Native使用了css-layout，实现了flexbox，Flexbox布局默认是线性布局即Android中的LinearLayout，PushActivity最外面由ScrollView包裹，style属性指定了该控件使用了哪种style，如果一个控件没有声明高度或宽度，则默认填充满父布局。使用StyleSheet.create来定义样式，可以看到样式定义所用的语法是JSON格式的：

    
    <code class="scala"><span class="hljs-keyword">var</span> styles = <span class="hljs-type">React</span>.<span class="hljs-type">StyleSheet</span>.create({
      container: {  
        flex: <span class="hljs-number">1</span>,
      },
    });</code>


flex:1这个也相当于match_parent。**需要注意的是，在React Native的样式中中，如果没有声明，数值的默认单位都是dp，而不是px**，下面讲解布局相关的属性。

flexDirection:指定排列方向，有row, column这两个值，分别是水平及垂直排列，默认为垂直排列。

_alignItems_: 可能的取值:



	
  * flex-start 控件在x轴的起点对齐；但在水平布局中为在y轴的起点对齐

	
  * center 控件在x轴的中点对齐；在水平布局中为在y轴的中点对齐

	
  * flex-end 控件在x轴的终点对齐；但在水平布局中为在y轴的终点对齐

	
  * stretch 默认值，占满容器的高度；如果为水平方向则占满宽度。


_justifyContent_: 可能的取值:



	
  * flex-start 在垂直布局中为上对齐（默认值）；在水平布局中为左对齐（默认值）

	
  * center 居中

	
  * flex-end 在垂直布局中为下对齐；在水平布局中为右对齐

	
  * space-between 在垂直布局中以高为基准，控件两端对齐，控件之间的间隔相等；在水平布局中以宽为基准，控件两端对齐，控件之间间隔相等

	
  * space-around 在垂直布局中以高为基准，控件之间的间隔相等，控件与边界的间隔为控件之间间隔的一半；在水平布局中以宽为基准，控件之间的间隔相等，控件与边界的间隔为控件之间间隔的一半


[![react native with jpush](/images/2016/04/1745101-9809fba386f579f7.png)](/images/2016/04/1745101-9809fba386f579f7.png)

_alignSelf_ 其效果与alignItems一样，但针对个别控件。如果某个控件声明了alignSelf属性，则会覆盖父容器的alignItems属性。

_position_ :可能的取值：



	
  * relative 相对位置（默认值）

	
  * absolute 绝对位置这两个属性一般会搭配left，top，right，bottom这4个属性来使用，分别表示控件的左上右下边缘距离y轴或x轴的距离。


还有一些属性是与控件相关的：

**背景颜色**



	
  1. backgroundColor //所有涉及颜色的都是字符串类型的rgb格式，如#ffffff


**边框**



	
  1. borderBottomWidth //底部边框宽度

	
  2. borderLeftWidth //左边边框宽度

	
  3. borderRightWidth //右边边框宽度

	
  4. borderTopWidth //顶部边框宽度

	
  5. borderWidth //所有边框宽度

	
  6. borderTopLeftRadius //左上圆角

	
  7. borderTopRightRadius //右上圆角

	
  8. borderBottomLeftRadius //左下圆角

	
  9. borderBottomRightRadius //右下圆角

	
  10. borderRadius //圆角

	
  11. borderBottomColor //底边框颜色

	
  12. borderLeftColor //左边框颜色

	
  13. borderRightColor //右边框颜色

	
  14. borderTopColor //上边框颜色

	
  15. borderColor //边框颜色


**外边距**



	
  1. marginBottom

	
  2. marginLeft

	
  3. marginRight

	
  4. marginTop

	
  5. marginVertical

	
  6. marginHorizontal

	
  7. margin


**内边距**



	
  1. paddingBottom

	
  2. paddingLeft

	
  3. paddingRight

	
  4. paddingTop

	
  5. paddingVerticalpaddingHorizontal

	
  6. padding


**宽高**



	
  1. width

	
  2. height


**字体相关**



	
  1. color 字体颜色

	
  2. fontFamily 字体族

	
  3. fontSize 字体大小

	
  4. fontStyle 字体样式，正常，倾斜等，值为enum('normal', 'italic')

	
  5. fontWeight 字体粗细，值为enum("normal", 'bold', '100', '200', '300', '400', '500', '600', '700', '800', '900')

	
  6. letterSpacing 字符间隔

	
  7. lineHeight 行高

	
  8. textAlign 字体对齐方式，值为enum("auto", 'left', 'right', 'center', 'justify')

	
  9. textDecorationLine 字体修饰，上划线，下划线，删除线，无修饰，值为enum("none", 'underline', 'line-through', 'underline underline-through')

	
  10. textDecorationStyle enum("solid", 'double', 'dotted', 'dashed') 修饰的线的类型

	
  11. textDecorationColor 修饰的线的颜色

	
  12. writingDirection enum("auto", 'ltr', 'rtl') 字体显示方向


**图片相关**



	
  1. resizeMode enum('cover', 'contain', 'stretch') conver是指按照图片实际大小显示，超出部分裁剪，默认值；contain是无论如何都将图片显示在控件中，如果超出则等比例缩小并居中显示；stretch是指将图片进行拉伸，填充满控件

	
  2. overflow enum('visible', 'hidden') 超出部分是否显示，hidden为隐藏

	
  3. tintColor 着色，rgb字符串类型

	
  4. opacity 透明度


在render()中，使用JSX的写法，用一对<View></View>标签来包裹一个控件，这相当于Html中的<div></div>标签。下面给出一些React Native与Android控件对照：
<table >

<tr >
**React Native**
**Android**
</tr>

<tbody >
<tr >

<td >Text
</td>

<td >TextView
</td>
</tr>
<tr >

<td >TouchableHighlight
</td>

<td >Button
</td>
</tr>
<tr >

<td >TextInput
</td>

<td >EditText
</td>
</tr>
<tr >

<td >image
</td>

<td >ImageView
</td>
</tr>
<tr >

<td >ScrollView
</td>

<td >ScrollView
</td>
</tr>
<tr >

<td >ListView
</td>

<td >ListView
</td>
</tr>
</tbody>
</table>
React Native还实现了一些比较常用的控件，如Switch、Picker、ToolbarAndroid、ViewPagerAndroid等。这些控件就不再一一讲解了，有很多文章可以参考，官网也有[demo](https://github.com/facebook/react-native/tree/master/Examples/UIExplorer)


## JS调用Native


下面来讲解一下JS如何调用Native。如果我们的应用是混合的React Native应用(使用了第三方jar，如本例中的jpush-sdk)，那么在JS中调用sdk中的接口是非常常见而且也是必要的。使用NativeModule就可以达到这种目的。要声明一个NativeModule，需要以下几个步骤。



	
  * **定义一个NativeModule类，继承自ReactContextBaseJavaModule。**

    
    <code class="scala">public <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">PushHelperModule</span> <span class="hljs-keyword"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">ReactContextBaseJavaModule</span> {</span>    
      <span class="hljs-keyword">private</span> static <span class="hljs-type">String</span> <span class="hljs-type">TAG</span> = <span class="hljs-string">"PushHelperModule"</span>;    
      <span class="hljs-keyword">private</span> <span class="hljs-type">Context</span> mContext;    
      public <span class="hljs-type">PushHelperModule</span>(<span class="hljs-type">ReactApplicationContext</span> reactContext) {        
        <span class="hljs-keyword">super</span>(reactContext);    
      }    
    
      <span class="hljs-annotation">@Override</span>    
      public boolean canOverrideExistingModule() {        
        <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>;    
      }        
    
      <span class="hljs-annotation">@Override</span>    
      public <span class="hljs-type">String</span> getName() {        
        <span class="hljs-keyword">return</span> <span class="hljs-string">"PushHelper"</span>;    
      }
    }</code>


声明一个NativeModule类时，需要重写canOverrideExistingModule()及getName()两个方法，在getName中返回的字符串即为在JS中引用的类，即在JS中使用PushHelper来引用PushHelperModule这个类。现在可以在PushHelperModule中添加ReactMethod了：

    
    <code class="java"><span class="hljs-annotation">@ReactMethod</span>    
    <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span><span class="hljs-params">(Callback successCallback, Callback errorCallback)</span> </span>{        
      <span class="hljs-keyword">try</span> {            
        JPushInterface.init(PushDemoApplication.getContext());            
        successCallback.invoke(<span class="hljs-string">"init Success!"</span>);            
        Log.i(<span class="hljs-string">"PushSDK"</span>, <span class="hljs-string">"init Success !"</span>);        
      } <span class="hljs-keyword">catch</span> (Exception e) {            
        errorCallback.invoke(e.getMessage());        
      }    
    }</code>





使用@ReactMethod标签来表明这是一个React方法，这样就可以在JS中直接调用，而且这个方法必须为public，返回类型为void。上面的方法还使用了Callback（com.facebook.react.bridge.Callback），CallBack可以用来在Native中回调JS中的方法。结合本例，init方法中调用了JPushInterface.init(context)这个接口，接着执行了callback.invoike()，这个方法就可以回调到JS了.invoke()可以带参数，String或者Map都行。可以有多个Callback，相应地在JS中要带多个参数。


<blockquote>push_activity.js</blockquote>



    
    <code class="coffeescript"> onInitPress() {    
        PushHelper.init( <span class="hljs-function"><span class="hljs-params">(success)</span> =></span> {      
          ToastAndroid.show(success, ToastAndroid.SHORT);    
        }, <span class="hljs-function"><span class="hljs-params">(error)</span> =></span> {      
          ToastAndroid.show(error, ToastAndroid.SHORT);    
        });  
    }</code>


在JS中使用() => {}箭头函数就可以接收回调函数了。



	
  * **新建一个类实现 ReactPackage，然后在将刚才定义的 NativeModule 加进来：**



    
    <code class="java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CustomReactPackage</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">ReactPackage</span> </span>{
        <span class="hljs-annotation">@Override</span>
        <span class="hljs-function"><span class="hljs-keyword">public</span> List<NativeModule> <span class="hljs-title">createNativeModules</span><span class="hljs-params">(ReactApplicationContext reactContext)</span> </span>{
            List<NativeModule> result = <span class="hljs-keyword">new</span> ArrayList<>();
            result.add(<span class="hljs-keyword">new</span> PushHelperModule(reactContext));
            result.add(<span class="hljs-keyword">new</span> ToastModule(reactContext));
            result.add(<span class="hljs-keyword">new</span> JSHelperModule(reactContext));
            <span class="hljs-keyword">return</span> result;
        }
    
        <span class="hljs-annotation">@Override</span>
        <span class="hljs-keyword">public</span> List<Class<? extends JavaScriptModule>> createJSModules() {
            <span class="hljs-keyword">return</span> Collections.emptyList();
        }
    
        <span class="hljs-annotation">@Override</span>
        <span class="hljs-function"><span class="hljs-keyword">public</span> List<ViewManager> <span class="hljs-title">createViewManagers</span><span class="hljs-params">(ReactApplicationContext reactContext)</span> </span>{
            List<ViewManager> result = <span class="hljs-keyword">new</span> ArrayList<>();
            result.add(<span class="hljs-keyword">new</span> ReactTextManager());
            <span class="hljs-keyword">return</span> result;
        }
    }</code>


在重写的 createNativeModules() 方法中将 PushHeperModule 加到List，然后返回这个List就行了。其他的模块如果为空可以返回Collections.emptyList()。



	
  * **在MainActivity中将packger加到ReactInstanceManager的构造方法中**



    
    <code class="scala">mReactInstanceManager = <span class="hljs-type">ReactInstanceManager</span>.builder()
                    .setApplication((<span class="hljs-type">Application</span>) <span class="hljs-type">PushDemoApplication</span>.getContext())
                    .setBundleAssetName(<span class="hljs-string">"index.android.bundle"</span>)
                    .setJSMainModuleName(<span class="hljs-string">"react-native-android/index.android"</span>)
                    .addPackage(<span class="hljs-keyword">new</span> <span class="hljs-type">MainReactPackage</span>())
                    .addPackage(<span class="hljs-keyword">new</span> <span class="hljs-type">CustomReactPackage</span>())
                    .setUseDeveloperSupport(<span class="hljs-type">BuildConfig</span>.<span class="hljs-type">DEBUG</span>)
                    .setInitialLifecycleState(<span class="hljs-type">LifecycleState</span>.<span class="hljs-type">RESUMED</span>)
                    .build();
            mReactRootView.startReactApplication(mReactInstanceManager, <span class="hljs-string">"PushDemoApp"</span>, <span class="hljs-literal">null</span>);</code>





	
  * **在JS中用NativeModules引用**



    
    <code class="scala"><span class="hljs-keyword">var</span> {
      <span class="hljs-type">NativeModules</span>,
    } = <span class="hljs-type">React</span>;
    <span class="hljs-keyword">var</span> <span class="hljs-type">PushHelper</span> = <span class="hljs-type">NativeModules</span>.<span class="hljs-type">PushHelper</span>;</code>


之后就可以使用PushHelper直接调用使用了@ReactMethod标签的方法了。需要注意的是，如果在Native添加了新的类，必须重新编译运行一次App，只是Reload JS是不能刷新的。


## Native 调用 JS


在Native中调用JS，最简单的方法是使用RCTDeviceEventEmitter。
打开MainActivity，在MessageReceiver的onReceive()中可以看到这段代码：

    
    <code class="css"><span class="hljs-tag">Assertions</span><span class="hljs-class">.assertNotNull</span>(<span class="hljs-tag">mReactInstanceManager</span><span class="hljs-class">.getCurrentReactContext</span>())        
      <span class="hljs-class">.getJSModule</span>(<span class="hljs-tag">DeviceEventManagerModule</span><span class="hljs-class">.RCTDeviceEventEmitter</span><span class="hljs-class">.class</span>)        
      <span class="hljs-class">.emit</span>("<span class="hljs-tag">receivePushMsg</span>", <span class="hljs-tag">messge</span>);</code>


通过getCurrentReactContext.getJSModule()方法找到RCTDeviceEventEmitter这个模块，然后调用emit方法调用JS中的方法，emit()方法的第一个参数即为在JS中定义的接收事件名，第二个参数可以传递一个对象到JS中。接下来在JS中接收这个事件：


<blockquote>push_activity.js</blockquote>



    
    <code class="coffeescript">componentWillMount() {
      DeviceEventEmitter.addListener(<span class="hljs-string">'receivePushMsg'</span>, <span class="hljs-function"><span class="hljs-params">(data)</span> =></span> {
            <span class="hljs-keyword">this</span>.setState({ <span class="hljs-attribute">pushMsg</span>: data });
          });
    }</code>


在组件注册的生命周期函数中使用DeviceEventEmitter.addListener()来接收RCTDeviceEventEmitter的emit事件即可。然后在组件卸载的生命周期函数中移除Listener：

    
    <code class="mathematica">componentWillUnmount() <span class="hljs-list">{
          DeviceEventEmitter.removeAllListeners();
    }</span></code>




## 运行


下面来说一下如何运行。

在init完Project后，就可以直接在命令行中使用


<blockquote>react-native run-android</blockquote>


命令运行了。这时会自动弹出一个窗口来执行React Packger。


[![react native with jpush](/images/2016/04/1745101-0ee69b2455da1775.jpeg)](/images/2016/04/1745101-0ee69b2455da1775.jpeg)





如果使用模拟器，推荐使用 Genymotion，只要注册一个账号就可以了。运行后如果出现


[![react native with jpush](/images/2016/04/1745101-081f574267b631fa.jpeg)](/images/2016/04/1745101-081f574267b631fa.jpeg)





不要慌，点击一下Reload JS 即可，如果出现了 Unable to download JS bundle 错误：


[![react native with jpush](/images/2016/04/1745101-0780398bf53543d7.jpeg)](/images/2016/04/1745101-0780398bf53543d7.jpeg)






首先检查一下设备和电脑的网络，确保两者在同一个Wifi环境下。在模拟器上点击下面的三角按钮，在弹出的菜单中点击Menu按钮，如图：


[![react native with jpush](/images/2016/04/1745101-793e44c39442774b.jpeg)](/images/2016/04/1745101-793e44c39442774b.jpeg)






就可以唤出设置对话框：


[![react native with jpush](/images/2016/04/1745101-bbec40e9580c5fc9.jpeg)](/images/2016/04/1745101-bbec40e9580c5fc9.jpeg)





在真机中，只需要晃动手机，即可弹出上面的对话框。然后点击Dev Settings选项进入设置界面。


[![react native with jpush](/images/2016/04/1745101-3edfab054ca201c1.jpeg)](/images/2016/04/1745101-3edfab054ca201c1.jpeg)






然后点击Debug server host & port for device，在弹出的对话框中，输入电脑连接的IP地址加上8081端口即可，如：


<blockquote>192.168.1.1:8081</blockquote>


然后点击OK，返回，重新唤出设置对话框，点击 Reload JS 选项，这时应该可以正确运行了。

有问题可发我邮件：caiyaoguan@gmail.com。后续会发表 ListView 的用法以及在 React Native 中如何使用 Redux 架构。

		
