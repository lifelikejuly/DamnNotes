# 一个推送带来的血案
由于国内特殊环境，Android平台的推送一直是百家齐放。很少有人会用到Google推出的GCM吧。但是如果你的客户来自国外，可能你就要考虑GCM或是FCM了。
# 你以为自己会用推送
虽然GCM推送集成不难，但真的在使用的时候才会发现问题。当集成GCM的应用在后台的时候，推送服务不会被我们拦截到而是走GCM内部通道，它会帮我们去做Notificaiton通知。所有就造成了如果没有配置好服务端的消息体就会频繁收到Push通知。
```
public class MyGcmListenerService extends GcmListenerService {

    public static final String TAG = "MyGcmListenerService";

    public static HashMap<String, Long> pushTimeMap = new HashMap<>();

    @Override
    public void onMessageReceived(String s, Bundle data) {
        Log.d(TAG, "GCM message received" + data.toString());
        PushModel push = new PushModel(s, data);
        GcmManager.getInstance().onReceive(push);
    }

}
```
如上所述的onMessageReceived方法只有当应用在前台才有效，在后台是完全靠服务端去做限流策略才能实现。
```
send_queue.append({'to': REGISTRATION_ID,
               'message_id': random_id(),
               "notification" : {
                  "body" : "Hello from Server! What is going on? Seems to work!!!",
                  "title" : "Hello from Server!",
                  "icon" : "@drawable/ic_school_white_48dp",
                  "sound": "default",
                  "color": "#03A9F4",
                  "click_action": "OPEN_MAIN_ACTIVITY"
                },
               'data': { 'message': "Hello" }})
```
例如跳转和各种推送内容的配置，总算是找到问题根源。最后就是服务端去处理的事了，完美甩锅！
# 参考
* https://developers.google.com/cloud-messaging/gcm#response
* https://stackoverflow.com/questions/31178029/gcmlistenerservice-onmessagereceived-not-called 