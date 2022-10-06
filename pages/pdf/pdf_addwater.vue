<template>
    <view class="content">
        <u--text type="primary" text="选择目标PDF文件" size='20' :bold='true'
        style='padding-left: 6%;padding-top: 5%;'></u--text>
        <u-row>
            <u-button  @click="openFile"  type="primary" shape="circle" text="打开文件选择器" style='width: 85%;margin-top: 25px;'></u-button>
        </u-row>
        
        <u--text type="primary" text="选择水印PDF文件" size='20' :bold='true'
        style='padding-left: 6%;padding-top: 5%;'></u--text>
        <u-row>
            <u-button  @click="openFile_2"  type="primary" shape="circle" text="打开文件选择器" style='width: 85%;margin-top: 25px;'></u-button>
        </u-row>
        
        <u-row style='margin-top: 7%;padding-left: 6%;padding-right: 6%;'><u--text type="primary" text='您选择的目标文件路径为:' size='20' bold
        ></u--text></u-row>
        <view style='margin-top: 3%;margin-left: 6%;margin-right: 6%;margin-bottom: 10%;font-size: 15px;' class="path">{{path}}</view>
        
        <u-row style='margin-top: 7%;padding-left: 6%;padding-right: 6%;'><u--text type="primary" text='您选择的水印文件路径为:' size='20' bold
        ></u--text></u-row>
        <view style='margin-top: 3%;margin-left: 6%;margin-right: 6%;margin-bottom: 10%;font-size: 15px;' class="path_2">{{path_2}}</view>
        
        <tki-file-manager ref="filemanager" @result="resultPath"></tki-file-manager>
        <tki-file-manager ref="filemanager_2" @result="resultPath_2"></tki-file-manager>
         
         <u--text type="primary" text="对添加水印后的文件重命名" size='20' :bold='true'
         style='padding-left: 6%;padding-top: 2%;'></u--text>
         <textarea class="bigbox" v-model="new_name" style="margin-left: 6%;margin-right: 6%;width:300px;height: 80px; padding: 20px; font-size: 30upx; color: #808080;"
          placeholder="请输入您要设置的新文件名" placeholder-style="color: #d1d1d1"></textarea>
         
        <u-button  @click="gotoDe" size="large" type="primary" shape="circle" text="开 始 添 加 水 印" style='width: 50%;margin-top: 30px;'></u-button>
        <u-popup :show="resShow" @close="close_popup" @open="open_popup" safeAreaInsetBottom mode="center" :round="10" customStyle='
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: center;
        width: 80%;
        padding: 20px;
        word-break: break-all;'>
            <u-row><u--text type="primary" text="已成功添加水印" size='20' :bold='true'
            style='width: 100%;height: 50upx;font-size: 40upx;margin-top: 30px;'></u--text></u-row>
            <u-row><u--text text="文件暂存位置:" size='14'
            style='height: 50upx;font-size: 40upx;margin-top: 30px;'></u--text></u-row>
            <u--text :text="filePath" size='14'
            style='font-size: 40upx;margin-top: 10px;margin-bottom: 45px;'></u--text>
            <view id="resBottom" style="margin-bottom: 25px;">
                <u-button  @click="resShow=false" size="large" shape="circle" text="返回" style='width: 40%;'></u-button>
                <u-button  @click="open" size="large" type="primary" shape="circle" text="打开文件" style='width: 40%;'></u-button>
            </view>
        </u-popup>
    </view>
</template>

<script>
import tkiFileManager from "@/components/tki-file-manager/tki-file-manager.vue"
import {
		pathToBase64,
		base64ToPath
	} from '../../js_sdk/gsq-image-tools/image-tools/index.js'
export default {
    data() {
        return {
            path: '暂未选择文件',
            path_2: '暂未选择文件',
            text: '',
            new_name:'',
            pdfBase64:'',
            resShow:false,
            filePath:'',
            temp_base64:''
        }
    },
    methods: {
        open_popup() {
                // console.log('open');
              },
        close_popup() {
                this.resShow = false
                // console.log('close');
              },
        gotoDe(){
            let this_temp=this
            if (this.path == "暂未选择文件") {
            	uni.showToast({
            		title: "请选择目标文件",
            		icon: "none",
            		mask: true
            	})
            } else if (this.path_2 == "暂未选择文件") {
            	uni.showToast({
            		title: "请选择水印文件",
            		icon: "none",
            		mask: true
            	})
            }  else if (this.new_name == "") {
            	uni.showToast({
            		title: "请输入添加水印后文件的新名字",
            		icon: "none",
            		mask: true
            	})
            }else {
                if( this.new_name.substr(-4)!='.pdf' ){ //结尾不是.pdf
                   this.new_name=this.new_name+'.pdf'
                   console.log(this.new_name);
                }
            	uni.showLoading({
            		title: '添加水印中……'
            	});
            	pathToBase64(this_temp.path)
            		.then(base64 => {
                        this_temp.temp_base64=base64.split(',')[1]
            		})
                pathToBase64(this_temp.path_2)
                	.then(base64 => {
                		uni.request({
                			url: this_temp.serverUrl + 'add_water',
                			method: 'POST',
                			header: {
                				'content-type': 'application/x-www-form-urlencoded'
                			},
                			data: {
                				ori_pdf: this_temp.temp_base64,
                				water_pdf: base64.split(',')[1],
                                new_pdf: this_temp.new_name
                			},
                			success: (res) => {
                				uni.hideLoading();
                				console.log(res)
                				if (res.statusCode == 200) {
                					uni.showToast({
                						title: '添加成功！',
                						mask: true
                					})
                                    console.log("http://121.5.146.129:9999"+res.data);
                                    uni.showLoading({
                                    	title: '下载到本地中……'
                                    });
                                    uni.downloadFile({
                                    	url: "http://121.5.146.129:9999"+res.data, 
                                    	success: function (res) {
                                            uni.hideLoading();
                                            console.log('下载成功');
                                            uni.showToast({
                                            	title: '下载成功！',
                                            	mask: true
                                            })
                                    	    this_temp.filePath = res.tempFilePath
                                            this_temp.resShow=true
                                    	},
                                        fail: () => {
                                            uni.hideLoading();
                                            console.log('下载失败');
                                            uni.showToast({
                                            	title: '下载失败',
                                            	mask: true
                                            })
                                        }
                                    });
                				} else {
                					uni.showToast({
                						title: '添加失败！',
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
                        uni.showToast({
                        	title: 'Android10+设备不允许读取非系统公共目录文件和系统公共目录的非媒体文件（音频文件、视频文件、图片文件），请移动至应用沙盒目录！',
                        	icon: 'none',
                        	mask: true
                        })
                        uni.hideLoading();
                	})
            }
        },
        open() {
            uni.openDocument({
              filePath: this.filePath,
              showMenu: true,
              success: function (res) {
                console.log('打开文档成功');
              }
            });
        },
        openFile(){
            this.$refs.filemanager._openFile()
        },
        openFile_2(){
            this.$refs.filemanager_2._openFile()
        },
        resultPath(e){
            this.path = e
        },
        resultPath_2(e){
            this.path_2 = e
        }
    },
    components: {
        tkiFileManager
    }
}
</script>
<style>
   .content {
        width: 100%;
/*        overflow: hidden; */
    }
    .bigbox {
    	width: 350px;
    	height: 200px;
    	margin: 50upx auto;
    	/* background-color: #f8f8f8; */
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
    
    #resBottom {
    	width: 100%;
    	display: flex;
    	flex-direction: row;
    	justify-content: space-around;
    }
</style>