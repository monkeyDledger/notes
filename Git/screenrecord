# Android录屏的三种解决方案

**本文总结三种用于安卓录屏的解决方案：**
1. adb shell命令`screenrecord`
2. `MediaRecorder`， `MediaProjection`
3. `MediaProjection` , `MediaCodec`和`MediaMuxer`

## screenrecord命令
> screenrecord是一个shell命令，支持**Android4.4**(API level 19)以上，录制的视频格式为**mp4** ,存放到手机sd卡里，默认录制时间为**180s**

````
adb shell screenrecord --size 1280*720 --bit-rate 6000000 --time-limit 30 /sdcard/demo.mp4
 --size 指定视频分辨率；
 --bit-rate 指定视频比特率，默认为4M，该值越小，保存的视频文件越小；
 --time-limit 指定录制时长，若设定大于180，命令不会被执行；
````

## MediaRecorder
> `MediaProjection`是**Android5.0**后才开放的屏幕采集接口，通过系统级服务`MediaProjectionManager`进行管理。

录屏过程可以分成两个部分，即通过MediaProjectionManage申请录屏权限，用户允许后开始录制屏幕；然后通过MediaRecorder对音视频数据进行处理。
1. 获取MediaProjectionManager实例
````java
MediaProjectionManager mProjectionManager = (MediaProjectionManager) getSystemService("media_projection");
````
2. 申请权限
```java
Intent captureIntent = mProjectionManager.createScreenCaptureIntent();
startActivityForResult(captureIntent, LOCAL_REQUEST_CODE);
```
`createScreenCaptureIntent()`这个方法会返回一个intent，你可以通过`startActivityForResult`方法来传递这个intent，为了能开始屏幕捕捉，activity会提示用户是否允许屏幕捕捉（为了防止开发者做一个木马，来捕获用户私人信息），你可以通过`getMediaProjection`来获取屏幕捕捉的结果。  

3. 在onActivityResult中获取结果
```java
@Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        MediaProjection mediaProjection = mProjectionManager.getMediaProjection(resultCode, data);
        if (mediaProjection == null) {
	    Log.e(TAG, "media projection is null");
	    return;
	}
        File file = new File("xx.mp4");  //录屏生成文件
        mediaRecord = new MediaRecordService(displayWidth, displayHeight, 6000000, 1, 
		    mediaProjection, file.getAbsolutePath());
	    mediaRecord.start();
}
```

4. 创建MediaRecorder进程

```java
package com.unionpay.service;

import android.hardware.display.DisplayManager;
import android.hardware.display.VirtualDisplay;
import android.media.MediaRecorder;
import android.media.projection.MediaProjection;
import android.util.Log;

public class MediaRecordService extends Thread {

    private static final String TAG = "MediaRecordService";

    private int mWidth;
    private int mHeight;
    private int mBitRate;
    private int mDpi;
    private String mDstPath;
    private MediaRecorder mMediaRecorder;
    private MediaProjection mMediaProjection;
    private static final int FRAME_RATE = 60; // 60 fps

    private VirtualDisplay mVirtualDisplay;

    public MediaRecordService(int width, int height, int bitrate, int dpi, MediaProjection mp, String dstPath) {
	mWidth = width;
	mHeight = height;
	mBitRate = bitrate;
	mDpi = dpi;
	mMediaProjection = mp;
	mDstPath = dstPath;
    }

    @Override
    public void run() {
	try {
	    initMediaRecorder();

	    //在mediarecorder.prepare()方法后调用
	    mVirtualDisplay = mMediaProjection.createVirtualDisplay(TAG + "-display", mWidth, mHeight, mDpi,
		    DisplayManager.VIRTUAL_DISPLAY_FLAG_PUBLIC, mMediaRecorder.getSurface(), null, null);
	    Log.i(TAG, "created virtual display: " + mVirtualDisplay);
	    mMediaRecorder.start();
	    Log.i(TAG, "mediarecorder start");
	} catch (Exception e) {
	    e.printStackTrace();
	}
    }

    /**
     * 初始化MediaRecorder
     * 
     * @return
     */
    public void initMediaRecorder() {
	mMediaRecorder = new MediaRecorder();
	mMediaRecorder.setVideoSource(MediaRecorder.VideoSource.SURFACE);
	mMediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
	mMediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
	mMediaRecorder.setOutputFile(mDstPath);
	mMediaRecorder.setVideoSize(mWidth, mHeight);
	mMediaRecorder.setVideoFrameRate(FRAME_RATE);
	mMediaRecorder.setVideoEncodingBitRate(mBitRate);
	mMediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.H264);
	mMediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);

	try {
	    mMediaRecorder.prepare();
	} catch (Exception e) {
	    e.printStackTrace();
	}
	Log.i(TAG, "media recorder" + mBitRate + "kps");
    }

    public void release() {
	if (mVirtualDisplay != null) {
	    mVirtualDisplay.release();
	    mVirtualDisplay = null;
	}
	if (mMediaRecorder != null) {
	    mMediaRecorder.setOnErrorListener(null);
	    mMediaProjection.stop();
	    mMediaRecorder.reset();
	    mMediaRecorder.release();
	}
	if (mMediaProjection != null) {
	    mMediaProjection.stop();
	    mMediaProjection = null;
	}
	Log.i(TAG, "release");
    }
}
```
## MediaCodec与MediaMuxer
`MediaCodec`提供对音视频压缩编码和解码功能，`MediaMuxer`可以将音视频混合生成多媒体文件，生成MP4文件。
与`MediaRecorder`类似，都需要先通过`MediaProjectionManager`获取录屏权限，在回调中进行屏幕数据处理。
这里创建另一个进程：
```java
package com.unionpay.service;

import java.io.IOException;
import java.nio.ByteBuffer;
import java.util.concurrent.atomic.AtomicBoolean;

import android.hardware.display.DisplayManager;
import android.hardware.display.VirtualDisplay;
import android.media.MediaCodec;
import android.media.MediaCodecInfo;
import android.media.MediaFormat;
import android.media.MediaMuxer;
import android.media.projection.MediaProjection;
import android.util.Log;
import android.view.Surface;

public class ScreenRecordService extends Thread{
    
    	private static final String TAG = "ScreenRecordService";

	private int mWidth;
	private int mHeight;
	private int mBitRate;
	private int mDpi;
	private String mDstPath;
	private MediaProjection mMediaProjection;
	// parameters for the encoder
	private static final String MIME_TYPE = "video/avc"; // H.264 Advanced
							     // Video Coding
	private static final int FRAME_RATE = 30; // 30 fps
	private static final int IFRAME_INTERVAL = 10; // 10 seconds between
						       // I-frames
	private static final int TIMEOUT_US = 10000;

	private MediaCodec mEncoder;
	private Surface mSurface;
	private MediaMuxer mMuxer;
	private boolean mMuxerStarted = false;
	private int mVideoTrackIndex = -1;
	private AtomicBoolean mQuit = new AtomicBoolean(false);
	private MediaCodec.BufferInfo mBufferInfo = new MediaCodec.BufferInfo();
	private VirtualDisplay mVirtualDisplay;

	public ScreenRecordService(int width, int height, int bitrate, int dpi, MediaProjection mp, String dstPath) {
	    super(TAG);
	    mWidth = width;
	    mHeight = height;
	    mBitRate = bitrate;
	    mDpi = dpi;
	    mMediaProjection = mp;
	    mDstPath = dstPath;
	}

	/**
	 * stop task
	 */
	public final void quit() {
	    mQuit.set(true);
	}

	@Override
	public void run() {
	    try {
		try {
		    prepareEncoder();
		    mMuxer = new MediaMuxer(mDstPath, MediaMuxer.OutputFormat.MUXER_OUTPUT_MPEG_4);

		} catch (IOException e) {
		    throw new RuntimeException(e);
		}
		mVirtualDisplay = mMediaProjection.createVirtualDisplay(TAG + "-display", mWidth, mHeight, mDpi,
			DisplayManager.VIRTUAL_DISPLAY_FLAG_PUBLIC, mSurface, null, null);
		Log.d(TAG, "created virtual display: " + mVirtualDisplay);
		recordVirtualDisplay();

	    } finally {
		release();
	    }
	}

	private void recordVirtualDisplay() {
	    while (!mQuit.get()) {
		int index = mEncoder.dequeueOutputBuffer(mBufferInfo, TIMEOUT_US);
//		Log.i(TAG, "dequeue output buffer index=" + index);
		if (index == MediaCodec.INFO_OUTPUT_FORMAT_CHANGED) {
		    // 后续输出格式变化
		    resetOutputFormat();

		} else if (index == MediaCodec.INFO_TRY_AGAIN_LATER) {
		    // 请求超时
//		    Log.d(TAG, "retrieving buffers time out!");
		    try {
			// wait 10ms
			Thread.sleep(10);
		    } catch (InterruptedException e) {
		    }
		} else if (index >= 0) {
		    // 有效输出
		    if (!mMuxerStarted) {
			throw new IllegalStateException("MediaMuxer dose not call addTrack(format) ");
		    }
		    encodeToVideoTrack(index);

		    mEncoder.releaseOutputBuffer(index, false);
		}
	    }
	}

	/**
	 * 硬解码获取实时帧数据并写入mp4文件
	 * 
	 * @param index
	 */
	private void encodeToVideoTrack(int index) {
	    // 获取到的实时帧视频数据
	    ByteBuffer encodedData = mEncoder.getOutputBuffer(index);

	    if ((mBufferInfo.flags & MediaCodec.BUFFER_FLAG_CODEC_CONFIG) != 0) {
		// The codec config data was pulled out and fed to the muxer
		// when we got
		// the INFO_OUTPUT_FORMAT_CHANGED status.
		// Ignore it.
		Log.d(TAG, "ignoring BUFFER_FLAG_CODEC_CONFIG");
		mBufferInfo.size = 0;
	    }
	    if (mBufferInfo.size == 0) {
		Log.d(TAG, "info.size == 0, drop it.");
		encodedData = null;
	    } else {
//		Log.d(TAG, "got buffer, info: size=" + mBufferInfo.size + ", presentationTimeUs="
//			+ mBufferInfo.presentationTimeUs + ", offset=" + mBufferInfo.offset);
	    }
	    if (encodedData != null) {
		mMuxer.writeSampleData(mVideoTrackIndex, encodedData, mBufferInfo);
	    }
	}

	private void resetOutputFormat() {
	    // should happen before receiving buffers, and should only happen
	    // once
	    if (mMuxerStarted) {
		throw new IllegalStateException("output format already changed!");
	    }
	    MediaFormat newFormat = mEncoder.getOutputFormat();
	    mVideoTrackIndex = mMuxer.addTrack(newFormat);
	    mMuxer.start();
	    mMuxerStarted = true;
	    Log.i(TAG, "started media muxer, videoIndex=" + mVideoTrackIndex);
	}

	private void prepareEncoder() throws IOException {

	    MediaFormat format = MediaFormat.createVideoFormat(MIME_TYPE, mWidth, mHeight);
	    format.setInteger(MediaFormat.KEY_COLOR_FORMAT, MediaCodecInfo.CodecCapabilities.COLOR_FormatSurface);
	    format.setInteger(MediaFormat.KEY_BIT_RATE, mBitRate);
	    format.setInteger(MediaFormat.KEY_FRAME_RATE, FRAME_RATE);
	    format.setInteger(MediaFormat.KEY_I_FRAME_INTERVAL, IFRAME_INTERVAL);

	    Log.d(TAG, "created video format: " + format);
	    mEncoder = MediaCodec.createEncoderByType(MIME_TYPE);
	    mEncoder.configure(format, null, null, MediaCodec.CONFIGURE_FLAG_ENCODE);
	    mSurface = mEncoder.createInputSurface();
	    Log.d(TAG, "created input surface: " + mSurface);
	    mEncoder.start();
	}

	private void release() {
	    if (mEncoder != null) {
		mEncoder.stop();
		mEncoder.release();
		mEncoder = null;
	    }
	    if (mVirtualDisplay != null) {
		mVirtualDisplay.release();
	    }
	    if (mMediaProjection != null) {
		mMediaProjection.stop();
	    }
	    if (mMuxer != null) {
		mMuxer.stop();
		mMuxer.release();
		mMuxer = null;
	    }
	}

}
```
该进程只实现了视频录制，调用该进程只需修改主进程中的onActivityResult方法。