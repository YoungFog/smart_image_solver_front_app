<template>
    <view class="bg" :style="[{ height: (windowHeight + statusBarHeight) + 'px' }]">
        <u--text type="primary" text="智能图像处理系统" size='26' :bold='true' 
        style='padding-left: 5%;padding-top: 17%;'></u--text>
        <u--text type="info" 
        text="Smart Image Solver" size='16' :bold='true' style='padding-left: 5%;padding-top: 5%;'></u--text>
        <view class="content" style="margin-top: 15%;">
           <u-button icon='edit-pen-fill' @click="encode" type="primary" shape="circle" text="图片 写入水印信息" style='width: 70%; height:9%'></u-button>
            <u-button icon='scan' @click="decode" type="primary" shape="circle" text="图片 读取水印信息" style='width: 70%;height:9%'></u-button>
            <u-button icon='lock-fill' @click="pdf_lock" type="primary" shape="circle" text='PDF 加 密' style='width: 70%;height:9%'></u-button>
            <u-button icon='lock-opened-fill' @click="pdf_unlock" type="primary" shape="circle" text='PDF 解 密' style='width: 70%;height:9%'></u-button>
            <u-button icon='bookmark-fill' @click="pdf_addwater" type="primary" shape="circle" text='PDF 添加水印' style='width: 70%;height:9%'></u-button>
        </view>
    </view>
</template>

<script>
	export default {
		data() {
			return {
				statusBarHeight: 0,
				windowHeight: 0,
			}
		},
		onBackPress() {
			uni.showModal({
				title: '提示',
				content: '是否退出应用？',
                cancelText: "确定", // 取消按钮的文字  
                confirmText: "取消", // 确认按钮的文字
				success: function(res) {
					if (res.cancel) {
						// 退出当前应用，改方法只在App中生效  
						plus.runtime.quit();
					} else if (res.confirm) {
						console.log('用户点击取消');
					}
				}
			});
			return true //return true的意思是禁止返回到上一个界面
		},
		onLoad() {
			setTimeout(() => {
				//获取状态栏高度，setTimeout后才能调用uni.
				uni.getSystemInfo({
					success: res => {
						this.statusBarHeight = res.statusBarHeight;
						this.windowHeight = res.windowHeight;
					}
				});
			}, 1);
		},
		methods: {
            encode() {
                uni.navigateTo({
                    url: '../encode/encode',
                })
            },
            decode() {
                uni.navigateTo({
                    url: '../decode/decode',
                })
            },
            pdf_lock(){
                uni.navigateTo({
                    url: '../pdf/pdf_lock',
                })
            },
            pdf_unlock(){
                uni.navigateTo({
                    url: '../pdf/pdf_unlock',
                })
            },
            pdf_addwater(){
                uni.navigateTo({
                    url: '../pdf/pdf_addwater',
                })
            }
		}
	}
</script>

<style>
    .bg{
        background-image: url('@/static/background.svg');
        background-position:top;
    }
    
	.content {
        height: 60%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-around;
	}

</style>
