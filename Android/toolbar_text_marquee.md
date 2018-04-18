# 酷炫跑马灯
由于项目组比较高大上涉及到国际化的语言文案，使得外文翻译的文字特别长。尤其是在Title中显示不下，开始的方案是使用setEllipsize为End就是省略后面无法显示的内容采用省略号。而万恶的资本主义还是希望能够全部显示整个文案，万幸的是TextView支持跑马灯的显示方式。所以我们就用这个来显示文字。
# 问题就在这
ToolBar标题TextView不能直接拿到对象，无法设置这个属性。所以在ToolBar中使用TextView隐藏内部的标题。然而设置的TextView却一直无法正常显示整个文本，一直是跑马灯加省略号的效果。遇到坑了！！错误示范代码如下：
```
<android.support.v7.widget.Toolbar
        android:id="@+id/toolbar_top_view"
        android:layout_width="match_parent"
        android:layout_height="@dimen/toolbar_height"
        android:background="?attr/status_bg_color"
        android:gravity="center_vertical"
        android:minHeight="?attr/actionBarSize"
        android:paddingBottom="0dp"
        android:paddingLeft="2dp"
        android:paddingRight="2dp"
        android:paddingTop="0dp"
        android:elevation="0dp"
        app:titleMarginTop="10dp">
        <TextView
            android:id="@+id/tv_title"
            style="@style/toolbar_action_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:layout_gravity="center"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:marqueeRepeatLimit="marquee_forever"
            android:textIsSelectable="true"
             />
    </android.support.v7.widget.Toolbar>
```

```
   mTvTitle = (TextView) mToolBar.findViewById(R.id.tv_title);
                if(mTvTitle != null){
                    mTvTitle.setEllipsize(TextUtils.TruncateAt.MARQUEE);
                    mTvTitle.setMarqueeRepeatLimit(-1);
                    mTvTitle.setFocusable(true);
                    mTvTitle.setFocusableInTouchMode(true);
                }
    protected void setTitle(String title) {
        if (mToolBar != null) {
            if(mTvTitle != null){

                mTvTitle.setText(title);
                mTvTitle.setSelected(true);
            }else{
                mToolBar.setTitle(title);
            }
        }
    }
```
试了好久终于OK了，正确姿势代码如下：
```

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar_top_view"
        style="@style/toolbar_action_text"
        android:layout_width="match_parent"
        android:layout_height="@dimen/toolbar_height"
        android:background="?attr/status_bg_color"
        android:gravity="center_vertical"
        android:minHeight="?attr/actionBarSize"
        android:paddingBottom="0dp"
        android:paddingLeft="2dp"
        android:paddingRight="2dp"
        android:paddingTop="0dp"
        android:elevation="0dp"
        app:titleMarginTop="10dp">
        <TextView
            android:id="@+id/tv_title"
            style="@style/toolbar_action_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:layout_gravity="center"
            android:singleLine="true"
             />
    </android.support.v7.widget.Toolbar>
```

```
    mTvTitle = (TextView) mToolBar.findViewById(R.id.tv_title);
                if(mTvTitle != null){
                    mTvTitle.setEllipsize(TextUtils.TruncateAt.MARQUEE);
                    mTvTitle.setMarqueeRepeatLimit(-1);
                }
    protected void setTitle(String title) {
        if (mToolBar != null) {
            if(mTvTitle != null){
                mTvTitle.setText(title);
                mTvTitle.setSelected(true);
            }else{
                mToolBar.setTitle(title);
            }
        }
    }
```

# 造成的原因
未完待续