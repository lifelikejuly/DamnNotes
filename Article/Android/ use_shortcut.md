# 兼容使用应用快捷方式
- 需要申请权限
```
 <uses-permission android:name="com.android.launcher.permission.INSTALL_SHORTCUT"/>
```
- 在国产rom中可能默认是关闭该权限设置无法动态申请，需要用户自行在系统设置中打开。
## 旧版本实现（7.0及以下）

```
public static final String ACTION_ADD_SHORTCUT = "com.android.launcher.action.INSTALL_SHORTCUT";

public void addShortcutBelowAndroidN(Context context) {
    Intent addShortcutIntent = new Intent(ACTION_ADD_SHORTCUT);

    // 不允许重复创建，不是根据快捷方式的名字判断重复的
    addShortcutIntent.putExtra("duplicate", false);

    addShortcutIntent.putExtra(Intent.EXTRA_SHORTCUT_NAME, "Shortcut Name");

    //图标
    addShortcutIntent.putExtra(Intent.EXTRA_SHORTCUT_ICON_RESOURCE, Intent.ShortcutIconResource.fromContext(context, R.mipmap.ic_shortcut));

    // 设置关联程序
    Intent launcherIntent = new Intent();
    launcherIntent.setClass(context, ShortcutActivity.class);
    addShortcutIntent.putExtra(Intent.EXTRA_SHORTCUT_INTENT, launcherIntent);

    // 发送广播
    context.sendBroadcast(addShortcutIntent);
}
```

## 高版本实现
高版本新增了ShortcutManager管理类，通过ShortCutManager去创建快捷入口
```
public static void addShortCut(Context context) {
      ShortcutManager shortcutManager = (ShortcutManager) context.getSystemService(Context.SHORTCUT_SERVICE);
      if(shortcutManager.isRequestPinShortcutSupported()) {
          Intent shortcutInfoIntent = new Intent(context, ShortcutActivity.class);
          shortcutInfoIntent.setAction(Intent.ACTION_VIEW);
          ShortcutInfo info = new ShortcutInfo.Builder(context, "The only id")
               .setIcon(Icon.createWithResources(context, R.mipmap.ic_shortcut))
               .setShortLabel("Short Label")
               .setIntent(shortcutInfoIntent);
               .build();
          //当添加快捷方式的确认弹框弹出来时，将被回调
          PendingIntent shortcutCallbackIntent = PendingIntent.getBroadcast(context, 0, new Intent(context, MyReceiver.class), PendingIntent.FLAG_UPDATE_CURRENT);
        shortcutManager.requestPinShortcut(info, shortcutCallbackIntent.getIntentSender());
      }
}
```
## 兼容方法
同时官方还提供一个兼容高低API的方法。
```
public static void addShortCutCompact(Context context) {
    if (ShortcutManagerCompat.isRequestPinShortcutSupported(context)) {
        Intent shortcutInfoIntent = new Intent(context, ShortcutActivity.class);
        shortcutInfoIntent.setAction(Intent.ACTION_VIEW); //action必须设置，不然报错

        ShortcutInfoCompat info = new ShortcutInfoCompat.Builder(context, "The only id")
                .setIcon(R.mipmap.ic_shortcut)
                .setShortLabel("Short Label")
                .setIntent(shortcutInfoIntent)
                .build();

        //当添加快捷方式的确认弹框弹出来时，将被回调
        PendingIntent shortcutCallbackIntent = PendingIntent.getBroadcast(context, 0, new Intent(context, MyReceiver.class), PendingIntent.FLAG_UPDATE_CURRENT);
        ShortcutManagerCompat.requestPinShortcut(context, info, shortcutCallbackIntent.getIntentSender());
    }
}
```

### 其他问题
- 通过小米9.0手机、谷歌9.0Nexus、oppo5.1手机测试发现谷歌原生系统在添加快捷方式会出现原生提示弹窗来告知用户是否允许添加，而在国产rom手机上并没有而是直接生效。
- 另外在原生手机中多次添加会在桌面生成重复同样的桌面快捷方式，我也找到了办法来避免这种情况。

```
if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.O){
    ShortcutManager shortcutManager = (ShortcutManagecontext.getSystemService(Context.SHORTCUT_SERVICE);
    List<ShortcutInfo> shortcutInfos = shortcutManager.getPinnedShortc();
    for(ShortcutInfo shortcutInfo : shortcutInfos){
        if(TextUtils.equals(shortcutInfo.getId(),"speech_cut")){
            return;
        }else{
            continue;
        }
    }
    createShortCut(context);
}
```

## Reference
https://github.com/OptimusPrimeRen/Android-O-Adaptive
https://github.com/googlesamples/android-AppShortcuts/#readme