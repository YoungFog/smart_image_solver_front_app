<template>
	<view style="display: flex;flex-direction: column;justify-content: center;align-items: center;text-align: center;">
        <u-row style='margin-top: 15%;'><u--text type="primary" text="选择要读取水印信息的图片" size='23' :bold='true'
        ></u--text></u-row>
        <u-row style='margin-top: 12%;margin-bottom: 5%;'><u--text type="info" text="您将通过输入图片\n从而获取图片中的水印信息" size='14'
        ></u--text></u-row>
		<view class="bigbox" @click="getImage()">
			<u-row>
			    <u-col><u-icon name="photo" color="#767676" size="35"></u-icon></u-col>
			</u-row>
			<u-row>
			    <u-col><u--text type="info" text="点击添加图片" size='15'></u--text></u-col>
			</u-row>
			<image id="img" :src="imgAdress"></image>
		</view>
        <u-button  @click="gotoDn" size="large" type="primary" shape="circle" text="开 始 读 取" style='width: 50%;margin-top: 30px;'></u-button>
        <view id="resBox" v-if="resShow">
        	<view id="res">
                <u-row><u--text type="primary" text="读取得到的水印信息" size='20' :bold='true'
                style='width: 100%;height: 50upx;font-size: 40upx;margin-top: 30px;'></u--text></u-row>
        		<view id="resImg" style="margin-top: 30px;margin-bottom: 30px;">
        			{{ word }}
        		</view>
        		<view id="resBottom">
                    <u-button  @click="resShow=false" size="large" shape="circle" text="返回" style='width: 40%;'></u-button>
                    <u-button  @click="saveWord" size="large" type="success" shape="circle" text="复制水印信息" style='width: 40%;'></u-button>
        		</view>
        	</view>
        </view>
	</view>
</template>

<script>
	import Vue from 'vue';
	import {
		pathToBase64,
		base64ToPath
	} from '../../js_sdk/gsq-image-tools/image-tools/index.js'
	export default {
		data() {
			return {
				imgAdress: '',
				resShow: false,
				word: ''
			}
		},
		methods: {
			getImage() {
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['original'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['camera', 'album'], //从相册选择
					success: (res) => {
						this.imgAdress = res.tempFilePaths[0];
					}
				});
			},
			gotoDn() {
				if (this.imgAdress == "") {
					uni.showToast({
						title: "请选择图片",
						icon: "none",
						mask: true
					})
				} else {
					uni.showLoading({
						title: '读取中……'
					});
					pathToBase64(this.imgAdress)
						.then(base64 => {
							uni.request({
								url: this.serverUrl + 'decode',
								method: 'POST',
								header: {
									'content-type': 'application/x-www-form-urlencoded'
								},
								data: {
									img: base64.split(',')[1]
								},
								success: (res) => {
									uni.hideLoading();
									if (res.statusCode == 200) {
										uni.showToast({
											title: '读取成功！',
											mask: true
										})
										this.resShow = true;
										this.word = uni.getStorageSync(res.data);
									} else {
										uni.showToast({
											title: '读取失败！',
											icon: 'none',
											mask: true
										})
									}
								},
								fail: (res) => {
									console.log(res);
									uni.hideLoading()
								}
							})
						})
						.catch(error => {
							uni.hideLoading();
						})
				}
			},
			saveWord() {
				uni.setClipboardData({
					data: this.word,
					success: (res) => {
						uni.showToast({
							title: "复制成功",
							icon: 'none',
							mask: true
						})
					}
				})
			}
		}
	}
</script>

<style>
	.bigbox {
		width: 350px;
		height: 350px;
		margin: 50upx auto;
		border: #C8C7CC 2upx solid;
		border-radius: 30upx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;

		font-size: 40upx;
		color: #d1d1d1;
		position: relative;
		overflow: hidden;
	}

	#img {
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
	}

	.button {
		width: 80%;
		height: 120upx;
		margin: 0 auto 100upx auto;
		background-color: #f8f8f8;
		border: #C8C7CC 2upx solid;
		border-radius: 30upx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}

	#resBox {
		width: 100%;
		height: 1300px;
		background-color: rgba(0, 0, 0, 0.3);
		position: fixed;
		top: 0;
		left: 0;
		z-index: 10;
	}

	#res {
		width: 725upx;
		margin: 0upx auto;
		background-color: #FFFFFF;
		border: #C8C7CC 2upx solid;
		transform: translateY(300upx);
		border-radius: 30upx;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: center;
	}

	#resImg {
		width: 500upx;
		height: 500upx;
		padding: 20upx;
		margin: 0 12.5upx;
		border: #dad9de 2upx solid;
		border-radius: 30upx;
		overflow: auto;
		word-break: break-all;
	}

	#resBottom {
		width: 100%;
		height: 80upx;
		margin-top: 20upx;
		margin-bottom: 50px;
		display: flex;
		flex-direction: row;
		justify-content: space-around;
	}

	.bottomButton {
		width: 40%;
		height: 100%;
		border-radius: 30upx;
		background-color: #b5b5b5;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		color: #FFFFFF;
	}
</style>
