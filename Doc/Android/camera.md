# 

# 
## Camera1
虽然Camera1已经过时，官方推荐使用Camera2，但这里还希望看一看Camera1的源码了解新版本和旧版本Camera之间的差别。
### 主要类成员
Camera1提供以下接口，常规相机功能的功能接口都提供了。意外的是我才发现Camera1就已经支持人脸识别功能，还是比较惊喜的。
```
    private ShutterCallback mShutterCallback; //快门回调
    private PictureCallback mRawImageCallback; //未加工图片回调
    private PictureCallback mJpegCallback; //jpg图片回调
    private PreviewCallback mPreviewCallback; //相机画面数据预览回调
    private PictureCallback mPostviewCallback; //相机画面数据预览回调
    private AutoFocusCallback mAutoFocusCallback; //自动对焦回调
    private AutoFocusMoveCallback mAutoFocusMoveCallback; //相机自动对焦开始或停止时调用
    private OnZoomChangeListener mZoomListener; //缩放变化回调
    private FaceDetectionListener mFaceListener; //人脸识别回调
    private ErrorCallback mErrorCallback; //错误回调
```
### 主要方法
这里大致列举Camera的公共方法，具体参[官方文档](https://developer.android.google.cn/reference/android/hardware/Camera)
```
    //拍照数据
    public final void takePicture(ShutterCallback shutter, PictureCallback raw,
            PictureCallback jpeg) {
        takePicture(shutter, raw, null, jpeg);
    }
    //预览数据
    public final void setPreviewCallbackWithBuffer(PreviewCallback cb) {
        mPreviewCallback = cb;
        mOneShot = false;
        mWithBuffer = true;
        if (cb != null) {
            mUsingPreviewAllocation = false;
        }
        setHasPreviewCallback(cb != null, true);
    }
    //开启人脸识别
    public final void startFaceDetection() {
        if (mFaceDetectionRunning) {
            throw new RuntimeException("Face detection is already running");
        }
        _startFaceDetection(CAMERA_FACE_DETECTION_HW);
        mFaceDetectionRunning = true;
    }
    //Camera参数配置
    public Parameters getParameters() {
        Parameters p = new Parameters();
        String s = native_getParameters();
        p.unflatten(s);
        return p;
    }
    //相机预览方向
    public native final void setDisplayOrientation(int degrees);
    //打开相机
    public static Camera open(int cameraId) {
        return new Camera(cameraId);
    }

```

这需要知道的一点是Camera1是通过继承Handler的EventHandler注册接收各种msg获取数据然后执行上面介绍的类成员接口。可以看到Camera1定义了Camera相关动作消息的静态常量，由底层CameraService服务上发动作接收执行动作，从而了解Camera1在Java层相当于是应用层，Camera绝大多数功能实现在底层完成，上层Java封装单纯提供方法接口为开发提供服务。
```
    // These match the enums in frameworks/base/include/camera/Camera.h
    private static final int CAMERA_MSG_ERROR            = 0x001;
    private static final int CAMERA_MSG_SHUTTER          = 0x002;
    private static final int CAMERA_MSG_FOCUS            = 0x004;
    private static final int CAMERA_MSG_ZOOM             = 0x008;
    private static final int CAMERA_MSG_PREVIEW_FRAME    = 0x010;
    private static final int CAMERA_MSG_VIDEO_FRAME      = 0x020;
    private static final int CAMERA_MSG_POSTVIEW_FRAME   = 0x040;
    private static final int CAMERA_MSG_RAW_IMAGE        = 0x080;
    private static final int CAMERA_MSG_COMPRESSED_IMAGE = 0x100;
    private static final int CAMERA_MSG_RAW_IMAGE_NOTIFY = 0x200;
    private static final int CAMERA_MSG_PREVIEW_METADATA = 0x400;
    private static final int CAMERA_MSG_FOCUS_MOVE       = 0x800;

    private class EventHandler extends Handler
    {
        private final Camera mCamera;

        public EventHandler(Camera c, Looper looper) {
            super(looper);
            mCamera = c;
        }

        @Override
        public void handleMessage(Message msg) {
            switch(msg.what) {
            case CAMERA_MSG_SHUTTER:
                if (mShutterCallback != null) {
                    mShutterCallback.onShutter();
                }
                return;
            ......
            default:
                Log.e(TAG, "Unknown message type " + msg.what);
                return;
            }
        }
    }
```
### 举个例子
下面例子中是Camera1简单使用,采用TextureView视图显示Camera的数据流，同样可以使用SurfaceView显示。关于TextureView和SurfaceView的不同可以单独写一篇文章讲解。
```
public class Camera1Activity extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        final TextureView textureView = new TextureView(this);
        setContentView(textureView);
        final Camera camera = Camera.open(Camera.CameraInfo.CAMERA_FACING_BACK);
        textureView.setSurfaceTextureListener(new TextureView.SurfaceTextureListener() {
            @Override
            public void onSurfaceTextureAvailable(SurfaceTexture surface, int width, int height) {
                try {
                    camera.setDisplayOrientation(90);
                    camera.setPreviewTexture(surface);
                    camera.startPreview();
                    camera.setPreviewCallback(new Camera.PreviewCallback() {
                        @Override
                        public void onPreviewFrame(byte[] data, Camera camera) {

                        }
                    });
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onSurfaceTextureSizeChanged(SurfaceTexture surface, int width, int height) {

            }

            @Override
            public boolean onSurfaceTextureDestroyed(SurfaceTexture surface) {
                return false;
            }

            @Override
            public void onSurfaceTextureUpdated(SurfaceTexture surface) {

            }
        });
    }
}
```
## Camera2


