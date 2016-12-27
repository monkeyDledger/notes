# video.js

## 支持http点播和rtmp流媒体的开源H5播放器

*html中引用
	`<video id="rtmp_player" class="video-js vjs-default-skin vjs-big-play-centered" controls preload="none" width="540" height="960"
       poster="/web/assets/img/background.jpg" data-setup="{}">
    <source src="../records/chenli1%20/test%20.mp4" type="video/mp4" />
    <source src="rtmp://172.20.6.162:1935/live/" type="rtmp/flv"/>
    </video>

    <script>
	    var player = videojs('rtmp_player');
	    player.ready(function () {
	        player.play();
	        player.currentSrc(rtmp_server);
	        player.currentType(rtmp_type);
	    });

	    //切换播放源
	    player.src(url);
        player.load(url);
    </script>`

*poster为封面图片

*[Api地址](http://docs.videojs.com/docs/api/player.html)