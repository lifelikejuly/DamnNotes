# 问题
在Intent里面传递Serializable对象的时候报错
```
Parcelable encountered IOException writing serializable object
```
开始没注意JumpBean的参数，查了资料才注意到原来有个Uri对象参数，Uri是实现Parcelable接口的，所以以serializable序列化传递JumpBean而且JumpBean实现Serializable接口，肯定是报错的。
```
public class JumpBean implements Serializable {
    public Uri uri;
    public String scheme;
    public String autyority;
    public String path;

    public void analysis(String url) {
        try {
            uri = Uri.parse(url);
            scheme = uri.getScheme();
            autyority = uri.getAuthority();
            path = uri.getPath();
        } catch (Exception e){
            e.printStackTrace();
        }
    }

    public String getParamByKey(String key) {
        return uri.getQueryParameter(key);
    }


    public void jump(Context context) {
        if (scheme != null && scheme.equals("jyhmdoctor")) {
            if (autyority != null) {
                if(path!=null){
                    autyority = autyority + path;
                }
                switch (autyority){
                    case "healthyWarming/warmingList"://预警
                        IntentUtils.goTask(context,"预警", Constant.TASK_ALERT);
                        break;

                    case "appointment/appointList"://约诊列表
                        IntentUtils.goMyAppoints(context);
                        break;

                    case "appointment/appointDetail"://约诊详情
                        IntentUtils.goAppointDetail(context,getParamByKey("appointId"));
                        break;

                    case "healthClassroom/classroomDetail"://健康课堂详情
                        IntentUtils.goHealthClassDetail(context, getParamByKey("classId"));
                        break;
                    default:
                        ToastUtils.show(context.getResources().getString(R.string.jump_error));
                        break;
                }
            } else {
                ToastUtils.show(context.getResources().getString(R.string.jump_error));
            }
        } else {
            ToastUtils.show(context.getResources().getString(R.string.jump_error));
        }
    }
}
```