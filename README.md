# Notification
Notification 填坑笔记
最最近项目在研究常驻通知栏，同时项目需要设置不同的消息声音。
虽然说网上资料关于通知很多，但是拿来就用还是有很多坑在里面。

> 1.常驻通知栏大家都比较熟悉<p>
> `NotificationCompat notification = new NotificationCompat.Builder(this);`
<p>
> `notification.setOngoing(true);`
<p>
> 就可以完成常驻,但是如果你在后面设置了
<p>
> `Notification build = notification.build();`
<p>
> ` build.flags = Notification.FLAG_SHOW_LIGHTS;`
> <p>或者其他的flags都会导致setOngoing()失效，而不能常驻。所以如果必须要设置flags，则继续加上比如：
<p>
> `build.flags = Notification.FLAG_SHOW_LIGHTS | Notification.FLAG_ONGOING_EVENT`
<p>同样的道理其他有关flags的设置也是一样的，这是因为这里设置的flags覆盖掉了前面的set方法。从源码里就可以看出里set方法是兼容以前与最新系统的方法，实际上调用的还是flags.<p>
>  <pre>
    private void setFlag(int mask, boolean value) {
        if (value) {
            mNotification.flags |= mask;
        } else {
            mNotification.flags &= ~mask;
        }
    }
 </pre>
>2.设置声音，声音文件什么都齐了，却一直没声音，很是无解，其他手机还有默认的，小米的死活默认的都没有。
>源代码是这样的：
><pre>
	notification.setDefaults(Notification.FLAG_SHOW_LIGHTS | Notification.DEFAULT_LIGHTS | Notification.DEFAULT_VIBRATE | Notification.DEFAULT_SOUND)；
	notification.setSound(Uri.parse("android.resource://" + getPackageName() + "/" + R.raw.alarm));
> </pre>
> 一看就知道了多设置个`Notification.DEFAULT_SOUND`导致setSound不生效。经过去掉，测试还是不行~~~，什么问题，再仔细瞅瞅
> 发现个多余设置Notification.FLAG_SHOW_LIGHTS，查看api:
<p>`.setDefaults(int defaults)`  （NotificationCompat.Builder中的方法，用于提示）
<p>功能：向通知添加声音、闪灯和振动效果的最简单、使用默认（defaults）属性，可以组合多个属性（和方法1中提示效果一样的）
>对应属性：

>- Notification.DEFAULT_VIBRATE    //添加默认震动提醒  需要 VIBRATE permission

>- Notification.DEFAULT_SOUND    // 添加默认声音提醒

>- Notification.DEFAULT_LIGHTS// 添加默认三色灯提醒

>- Notification.DEFAULT_ALL// 添加默认以上3种全部提醒

> 只有四个属性，去掉多余后，测试生效！
> 但是那个属性是啥呢？
> 看看api：
>
1）只有在设置了标志符Flags为Notification.FLAG_SHOW_LIGHTS的时候，才支持三色灯提醒。<p>
>2）这边的颜色跟设备有关，不是所有的颜色都可以，要看具体设备。<p>
>.setLights(0xff0000ff, 300, 0)
同理，以下方法也可以设置同样效果：
><pre>
	Notification notify = mBuilder.build();
    notify.flags = Notification.FLAG_SHOW_LIGHTS;
    notify.ledARGB = 0xff0000ff;
    notify.ledOnMS = 300;
    notify.ledOffMS = 300;
></pre>
>经过测试红米note3死活不生效。

太忙了，先写这里，填坑继续中。。。
