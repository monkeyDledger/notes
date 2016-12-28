# video.js

## 支持http点播和rtmp流媒体的开源H5播放器

*html中引用

	<video id="rtmp_player" class="video-js vjs-default-skin vjs-big-play-centered" controls preload="none" width="540" height="960"
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
    </script>

*poster为封面图片

*Api地址 [http://docs.videojs.com/docs/api/player.html]()

*使用过程中，播放rtmp流数据时遇到问题，画面初始大小存在问题，手动全屏后才正常

#GrindPlayer
## [官网](http://osmfhls.kutu.ru/docs/grind)

	<!DOCTYPE html>
	<html>
	<head>
	    <title>Grind Player</title>
	    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
	    <script type="text/javascript" src="http://yandex.st/swfobject/2.2/swfobject.min.js"></script>
	    <script type="text/javascript">
	        var flashvars = {
	            src: "YOUR SOURCE URL HERE"
	        };
	        var params = {
	            allowFullScreen: true
	            , allowScriptAccess: "always"
	            , bgcolor: "#000000"
	        };
	        var attrs = {
	            name: "player"
	        };
	
	        swfobject.embedSWF("GrindPlayer.swf", "player", "854", "480", "10.2", null, flashvars, params, attrs);
	    </script>
	</head>
	<body>
	    <div id="player"></div>
	</body>
	</html>