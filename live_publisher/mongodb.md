# MongoDb相关

* bin路径添加到环境变量，命令行输入mongod --dbpath=D:\installed\mongodb2.6.12\data\db 
* nodejs引用mongodb的方法：
	`var mongoose = require('mongoose');
	mongoose.Promise = global.Promise;
	mongoose.connect('mongodb://localhost/autoApp');
	exports.mongoose = mongoose;

	//db controller
	const mongdb = require('./mongodb');
	const Schema = mongdb.mongoose.Schema;

	const videoSchema = new Schema(
	    {
	        videoName: String,
	        publisherName: String,
	        publishTime: String,
	        introduction: String
	    },
	    {
	        collection: 'videos'
	    }
	);

	const video = mongdb.mongoose.model("video", videoSchema);`