

- [Task](http://blog.csdn.net/zhangjg_blog/article/details/10923643) ： task是一组相互关联的activity的集合，task是可以跨应用的，这正是task存在的一个重要原因。有的Activity，虽然不在同一个app中，但为了保持用户操作的连贯性，把他们放在同一个任务中。例如，在我们的应用中的一个Activity A中点击发送邮件，会启动邮件程序的一个Activity B来发送邮件，这两个activity是存在于不同app中的，但是被系统放在一个任务中，这样当发送完邮件后，用户按back键返回，可以返回到原来的Activity A中，这样就确保了用户体验。
(back stack)

- **launchMode**（android:launchMode） [demo](https://github.com/aaronnju/Activities-LaunchMode-demo)
    - **standard:**每次跳转系统都会在task中生成一个新的FirstActivity实例，并且放于栈结构的顶部，当我们按下后退键时，才能看到原来的
    - **singTop:**如果发现有对应的Activity实例正位于栈顶，则重复利用，不再生成新的实例，onNewIntent()被发送到现有的Activity。这种启动模式的用例之一就是**搜索详情页**功能，如果栈顶已经有一个SearchActivity，我们将Intent发送给现有的activity，让它来更新搜索结果。这样就只会有一个在栈顶的SearchActivity，只需点一次back就可以回到之前的activity。singleTop和它的调用者处在一个任务中。
    - **singTask:**系统总会在一个新任务(把启动模式设置为singleTask，framework在启动该activity时只会把它标示为可在一个新任务中启动，至于是否在一个新任务中启动，还要受其他条件的限制。`android:taskAffinity=""`)的最底部（root）启动这个activity，并且被这个activity启动的其他activity会和该activity同时存在于这个新任务中。如果系统中已经存在这样的一个activity则会重用这个实例，并且调用他的onNewIntent()方法。即，这样的一个activity在系统中只会存在一个实例。这种模式的应用案例有。**邮件客户端的收件箱**或者社交网络的时间轴。这些Activity一般不会设计成拥有多个实例，singleTask可以满足。但是在使用这种模式的时候必须要明智，因为有些Activity会在用户不知情的情况下被销毁。再举一个例子，Android系统**内置的浏览器**程序声明自己浏览网页的Activity始终应该在一个独立的任务当中打开，也就是通过在<activity>元素中设置"singleTask"启动模式来实现的。这意味着，当你的程序准备去打开Android内置浏览器的时候，新打开的Activity并不会放入到你当前的任务中，而是会启动一个新的任务。而如果浏览器程序在后台已经存在一个任务了，则会把这个任务切换到前台。
    - **singleInstance:**它会启用一个新的栈结构，将Acitvity放置于这个新的栈结构中，并保证不再有其他Activity实例进入。也就是说，这种Activity所在的任务中始终只会有一个Activity，通过这个Activity再打开的其它Activity也会被放入到别的任务当中。举例如**share应用**
	- [understand-android-activity-launchmode](https://inthecheesefactory.com/blog/understand-android-activity-launchmode/en)
