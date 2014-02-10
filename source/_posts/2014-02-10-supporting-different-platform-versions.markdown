---
layout: post
title: "在Android程序中如何支持不同平台版本"
date: 2014-02-10 16:35:31 +0800
comments: true
categories: Android
---
&emsp;&emsp;新版本的Android经常会提供大量新的API，但是我们仍然需要为那些没有升级的设备中的旧版本的Android提供支持。这篇文章会告诉大家如何在继续支持旧版本的同时使用最新的API。  <!--more-->  
&emsp;&emsp;Android dashboard会根据访问Google Play Store的设备数量来定期更新运行不同Android版本的激活设备的占有率。一般来说，通常的做法是首先让自己的app能支持90%左右的激活设备，在此基础上再针对新系统制作对应的版本。    
&emsp;&emsp;**提示：**为了在Android的各种版本中让app的特色和功能都得到体现，应该使用<font color="#3299CC">Android Support Library</font>，这样你就可以在旧版本的系统中使用一些新版本中才支持的API。  

**指定Minimum和Target API级别**
<hr>
&emsp;&emsp;<font color="#3299CC">AndroidManifest.xml</font>描述了app的详细信息，并且确定了它所支持的Android版本。<font color="#426F42"><uses-sdk</font>元素中的<font color="#426F42">minSdkVersion</font>指定了你的app所能兼容的最低API级别，<font color="#426F42">targetSdkVersion</font>指定了你设计和测试时所用的最高版本的API级别。  

例如：
``` xml+cheetah
<manifest xmlns:android="http://schemas.android.com/apk/res/android" ... >
     <uses-sdk android:minSdkVersion="4" android:targetSdkVersion="15" />
     ... 
</manifest>
```  
&emsp;&emsp;当新版本的Android发布的时候，一些风格和行为可能会改变。为了让你的应用程序利用这些改变，并且确保你的应用程序适应每种设备的风格，你应该设置<font color="#426F42">targetSdkVersion</font>的值以匹配最新的Android版本。  

**在运行时检测系统版本**
<hr>
&emsp;&emsp;Android在常量类Build中为每个平台版本都定义了一个识别码。在代码中使用这些识别码来加入判断条件，从而确保使用了更高级别API的那部分代码只会运行在提供了这些API的系统之上。
``` java
private void setUpActionBar() {     
	// Make sure we're running on Honeycomb or higher to use ActionBar APIs     
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {         
		ActionBar actionBar = getActionBar();         
		actionBar.setDisplayHomeAsUpEnabled(true);     
	} 
}
```
&emsp;&emsp;**注意：**在解析XML资源的时候，Android会忽略当前设备不支持的XML属性。因此，你可以安全地使用只有新版本才支持的XML属性，而不必担心在旧版本中遇到这些代码会出错。举个例子来说，如果在代码中设置<font color="#426F42">targetSdkVersion="11"</font>，那么在Android3.0或以上版本的系统中，你的应用程序就会默认包含<font color="#3299CC">ActionBar</font>。为了将菜单选项添加到ActionBar中，你需要在菜单资源XML文件中设置<font color="#426F42">android:showAsAction="ifRoom"</font>。在跨版本的XML中这样做是安全的，因为旧的Android版本中会忽略<font color="#426F42">showAsAction</font>这个属性（也就是说，你不用单独在<font color="#426F42">res/menu-v11/</font>中特别提供一个单独的版本)。  

**如何使用平台风格和主题**
<hr>
&emsp;&emsp;Android提供了用户体验方面的主题，使应用程序拥有和底层系统统一的视觉体验。这些主题可以通过manifest文件应用到你的app中。在使用这些内建的风格和主题之后，每次新版本的Android发布时，你的app就会很自然地具有最新的视觉体验。  

&emsp;&emsp;让你的Activity看起来象对话框:
``` xml+cheetah
<activity android:theme="@android:style/Theme.Dialog">
```  
&emsp;&emsp;让你的Activity有透明背景:
``` xml+cheetah
<activity android:theme="@android:style/Theme.Translucent">
```  
&emsp;&emsp;使用自己在<font color="#426F42">/res/values/styles.xml</font>中定义的主题：
``` xml+cheetah
<activity android:theme="@style/CustomTheme">
```  
&emsp;&emsp;如果需要在整个app（所有的activity）中使用一个主题，可以在<font color="#3299CC">&lt;application></font>中添加<font color="#426F42">android:theme</font>：
``` xml+cheetah
<application android:theme="@style/CustomTheme">
```  
&emsp;&emsp;想了解更多关于创建和使用主题的信息，可以参考 <a href="http://developer.android.com/guide/topics/ui/themes.html">Styles and Themes</a>。 
