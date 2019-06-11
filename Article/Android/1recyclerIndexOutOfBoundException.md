# 问题
RecyclerView在添加数据的时候有很小的概率会造成崩溃，代码如下：
```
    public void addMessage(MessageItem messageItem) {
        if (messageItem == null) return;
        messageItemList.add(messageItem);
        adapter.notifyItemInserted(messageItemList.size() - 1);
        rvChat.smoothScrollToPosition(Math.max(messageItemList.size() - 1, 0));
    }
```
崩溃日志
```
java.lang.IndexOutOfBoundsException: Inconsistency detected. Invalid view holder adapter positionViewHolder{363e4f51 position=16 id=-1, oldPos=15, pLpos:15 scrap [attachedScrap] tmpDetached no parent}
	at android.support.v7.widget.RecyclerView$l.a(Unknown Source)
	at android.support.v7.widget.RecyclerView$l.a(Unknown Source)
	at android.support.v7.widget.RecyclerView$l.c(Unknown Source)
	at android.support.v7.widget.LinearLayoutManager$c.a(Unknown Source)
	at android.support.v7.widget.LinearLayoutManager.a(Unknown Source)
	at android.support.v7.widget.LinearLayoutManager.a(Unknown Source)
	at android.support.v7.widget.LinearLayoutManager.c(Unknown Source)
	at android.support.v7.widget.RecyclerView.dispatchLayoutStep1(Unknown Source)
	at android.support.v7.widget.RecyclerView.dispatchLayout(Unknown Source)
	at android.support.v7.widget.RecyclerView.consumePendingUpdateOperations(Unknown Source)
	at android.support.v7.widget.RecyclerView.access$400(Unknown Source)
	at android.support.v7.widget.RecyclerView$s.run(Unknown Source)
	at android.view.Choreographer$CallbackRecord.run(Choreographer.java:826)
	at android.view.Choreographer.doCallbacks(Choreographer.java:622)
	at android.view.Choreographer.doFrame(Choreographer.java:590)
	at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:812)
	at android.os.Handler.handleCallback(Handler.java:815)
	at android.os.Handler.dispatchMessage(Handler.java:104)
	at android.os.Looper.loop(Looper.java:194)
	at android.app.ActivityThread.main(ActivityThread.java:5692)
	at java.lang.reflect.Method.invoke(Native Method)
	at java.lang.reflect.Method.invoke(Method.java:372)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:959)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:754)
```
原因是list数据的添加/删除 要与adapter的添加/删除同步。list数据发生变化，adapter还没来得及更新,list数据又发生变化，就会造成崩溃。

参考资料

[http://www.ithao123.cn/content-10861747.html](http://www.ithao123.cn/content-10861747.html)

[https://drakeet.me/recyclerview-bug-indexoutofboundsexception-inconsistency-detected-invalid-item-position-solution](https://drakeet.me/recyclerview-bug-indexoutofboundsexception-inconsistency-detected-invalid-item-position-solution)

[https://code.google.com/p/android/issues/detail?id=77846#c32](https://code.google.com/p/android/issues/detail?id=77846#c32)