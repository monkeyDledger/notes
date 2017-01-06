# SmarterStreamingSDK

## Android Publisher使用总结
### SDK引用步骤
1. libs文件夹导入至项目根目录，添加至Build Path
2. 将demo中的SmartPublisherJni.java复制到项目中
3. 在引用的Activity中通过静态加载方法加载so文件
`static {System.load("libSmartPublisher.so");}`

### 接口调用说明
1. 初始化
```java
/**
     * Initialized publisher.
     *
     * @param ctx:
     *            get by this.getApplicationContext()
     * 
     * @param audio_opt:
     *            if with 0: it does not publish audio; if with 1, it publish
     *            audio; if with 2, it publish external encoded audio, only
     *            support aac.
     * 
     * @param video_opt:
     *            if with 0: it does not publish video; if with 1, it publish
     *            video; if with 2, it publish external encoded video, only
     *            support h264, data:0000000167....
     * 
     * @param width:
     *            capture width; height: capture height.
     *
     *            <pre>
     * This function must be called firstly.
     *            </pre>
     *
     * @return {0} if successful
     */
    public native int SmartPublisherInit(Object ctx, int audio_opt, int video_opt, int width, int height);
```
2. 注册监听回调
```java
/**
     * Set callback event
     * 
     * @param callback
     *            function
     * 
     * @return {0} if successful
     */
    public native int SetSmartPublisherEventCallback(SmartEventCallback callback);
```

3. 录像保存及路径设置
```java
/**
     * Set if recorder the stream to local file.
     * 
     * @param isRecorder:
     *            (0: do not recorder; 1: recorder)
     * 
     *            <pre>
     *  NOTE: If set isRecorder with 1: Please make sure before call SmartPublisherStartPublish(), set a valid path via SmartPublisherCreateFileDirectory().
     *            </pre>
     * 
     * @return {0} if successful
     */
    public native int SmartPublisherSetRecorder(int isRecorder);

    /**
     * Create file directory
     * 
     * @param path,
     *            E.g: /sdcard/daniulive/rec
     * 
     *            <pre>
     *  The interface is only used for recording the stream data to local side.
     *            </pre>
     * 
     * @return {0} if successful
     */
    public native int SmartPublisherCreateFileDirectory(String path);

    /**
     * Set recorder directory.
     * 
     * @param path:
     *            the directory of recorder file.
     * 
     *            <pre>
     *  NOTE: make sure the path should be existed, or else the setting failed.
     *            </pre>
     * 
     * @return {0} if successful
     */
    public native int SmartPublisherSetRecorderDirectory(String path);

    /**
     * Set the size of every recorded file.
     * 
     * @param size:
     *            (MB), (5M~500M), if not in this range, set default size with
     *            200MB.
     * 
     * @return {0} if successful
     */
    public native int SmartPublisherSetRecorderFileMaxSize(int size);
```
4. 推流地址设定（rtmp://xxx:xx:xx:xx:1935/）
```java
/**
     * Set publish stream url.
     * 
     * if not set url or url is empty, it will not publish stream
     *
     * @param url:
     *            publish url.
     *
     * @return {0} if successful
     */
    public native int SmartPublisherSetURL(String url);
```
5. 开始推流
```java
/**
     * Start publish stream
     *
     * @return {0} if successful
     */
    public native int SmartPublisherStart();
```
6. 推流数据来源
**录屏， 解析ImageReader返回的Image对象**
```java
/**
     * Set live video data(no encoded data).
     *
     * @param data:
     *            RGBA data
     * 
     * @param rowStride:
     *            stride information
     * 
     * @param width:
     *            width
     * 
     * @param height:
     *            height
     *
     * @return {0} if successful
     */
    public native int SmartPublisherOnCaptureVideoRGBAData(ByteBuffer data, int rowStride, int width, int height);
```
**摄像头 通过Camera预览获取的视频帧**
```java
/**
     * Set live video data(no encoded data).
     *
     * @param cameraType:
     *            CAMERA_FACING_BACK with 0, CAMERA_FACING_FRONT with 1
     * 
     * @param curOrg:
     *            LANDSCAPE with 0, PORTRAIT 1
     *
     * @return {0} if successful
     */
    public native int SmartPublisherOnCaptureVideoData(byte[] data, int len, int cameraType, int curOrg);
```
## 音频捕捉
`NTAudioRecord`类, 开始推流后，调用音频初始化方法：
```java
/**
     * 音频捕捉初始化
     */
    private void initAudioRecord() {
	if (audioRecord == null) {
	    audioRecord = new NTAudioRecord(mContext, 1);
	} 
	
	if (audioRecord != null){
	    audioRecord.executeAudioRecordMethod();
	}
    }
```
