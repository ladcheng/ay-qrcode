<template>
	<view :class="modal?'show':'hide'">
		<view :style="{'margin-left':  marginLeft + 'px'}">
			<canvas class="canvas" style="width: 550rpx;height: 550rpx;" :canvas-id="qrcode_id"></canvas>
			<!-- <image mode="scaleToFill" :src="imagePath"></image> -->
		</view>
	</view>
</template>

<script>
	var qr_we = require("./qrcode_wx.js");
	const qrCode = require('./weapp-qrcode.js')
	export default {
		data() {
			return {
				show: true,
				imagePath: '',
				qrcode_id: 'qrcode_id',
				marginLeft : 0,
			}
		},
		props: {
			modal: {
				type: Boolean,
				default: false
			},
			url: {
				type: String,
				default: ''
			}
		},
		created: function() {
			let _this = this ;
			try {
				let isAndroid = false ;
			    const res = uni.getSystemInfoSync();
			    if(res.platform == 'android'){
					isAndroid = true ;
				}else{
					isAndroid = false ;
				}
				//app苹果二维码不居中
				//#ifdef APP-PLUS
				if (!isAndroid) {
					_this.marginLeft = 46;
				}
				// #endif
				
			} catch (e) {
			    // error
			}
			
			//#ifdef MP-WEIXIN
			_this.marginLeft = 40;
			// #endif
			
		},
		methods: {
			
			hideQrcode() {
				this.$emit("hideQrcode")
			},
			// 二维码生成工具
			crtQrCode() {
				let _this = this;
				//#ifndef MP-WEIXIN
				new qrCode(_this.qrcode_id, {
					text: this.url,
					width: 260,
					height: 260,
					colorDark: "#333333",
					colorLight: "#FFFFFF",
					correctLevel: qrCode.CorrectLevel.H
				})
				// #endif
				//#ifdef MP-WEIXIN
				_this.createQrCode(this.url, _this.qrcode_id, 260, 260);
				// #endif
			},
			//#ifdef MP-WEIXIN

			createQrCode: function(url, canvasId, cavW, cavH) {
				//调用插件中的draw方法，绘制二维码图片
				qr_we.api.draw(url, canvasId, cavW, cavH, this, this.canvasToTempImage);
				// setTimeout(() => { this.canvasToTempImage();},100);

			},
			//获取临时缓存照片路径，存入data中
			canvasToTempImage: function() {
				var _this = this;
				// uni.canvasToTempFilePath({
				// 	canvasId: _this.qrcode_id,
				// 	success: function(res) {
				// 		var tempFilePath = res.tempFilePath;
				// 		console.log(tempFilePath);
				// 		_this.imagePath = tempFilePath;
				// 	},
				// 	fail: function(res) {
				// 		console.log(res);
				// 	}
				// }, _this);
			},
			// #endif
		},
		mounted() {}
	}
</script>

<style scoped lang="scss">
	.qrcode-box {
		position: fixed;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		height: 100vh;
		width: 100vw;
		background-color: rgba(59, 59, 59, 0.6);
		// opacity: 0.8;
		text-align: center;
		display: flex;
		align-items: center;
		display: none;

		.qrcode-item {
			flex: 1;
			position: relative;
			text-align: center;

			.item-box {
				width: 90%;
				margin: auto;
				display: inline-block;
				margin-top: 30%;
				padding-bottom: 30rpx;

				// animation: show 0.7s;
				.title {
					font-size: 46rpx;
					text-align: center;
					margin-bottom: 24rpx;
				}

				.canvas {
					margin: auto;
					display: inline-block;
					margin: auto;
				}

				background-color: #FFFFFF;
			}

		}
	}

	.opacity {
		opacity: 0;
		display: block;
	}

	.show {
		display: block;
		animation: fade 0.7s;

		// -moz-animation: fade 0.5s; /* Firefox */
		// -webkit-animation: fade 0.5s; /* Safari 和 Chrome */
		// -o-animation: fade 0.5s;
	}

	.hide {
		animation: hide 0.7s;
	}

	@keyframes fade {
		from {
			opacity: 0.8;
		}

		to {
			opacity: 1;
		}
	}

	@keyframes hide {
		from {
			opacity: 1;
		}

		to {
			opacity: 0;
		}
	}
</style>
