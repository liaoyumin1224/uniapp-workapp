<template>
	<!-- 意见反馈模块 -->
	<view class="feedback">
		<!-- 意见反馈 -->
		<view class="feedback-head">
			<uni-icons class="icon" @click="gohome" type="left" size="25" weight="600" style="font-weight: bold;"></uni-icons>
			<h2>意见反馈</h2>
			<p @click="gohistory">历史建议</p>
		</view>
		<!-- 提交成功界面 -->
		<view v-if="isSubmitted" class="success-submit">
			<image src="/common/images/pages/矢量智能对象.jpg"></image>
			<p>！提交成功</p>
			<span>您反馈的意见物品们已收到，请耐心等待处理结果</span>
		</view>
		<!-- 表单提交界面 -->
		<view v-else>
			<!-- 反馈类型 -->
			<view class="feedback-type">
				<p>反馈类型</p>
				<!-- openTypePicker 打开选择器 -->
				<view class="type-value" @click="openPopup">
					{{ displayType }}
					<uni-icons type="down" size="15"></uni-icons>
				</view>
			</view>
			
			<!-- 自定义底部弹窗 -->
			<!-- @maskClick="closePopup"点击遮盖层关闭弹窗 -->
			<uni-popup  ref="popup" type="bottom" @maskClick="closePopup">
				<view class="popup-container">
					<p>反馈类型</p>
					<view class="popup-list">
						<view v-for="(item,index) in typeOptions" :key="index"
						@click="pickType(item)">
							<text>{{ item }}</text>
						</view>
					</view>
				</view>
			</uni-popup>
			<!-- 意见说明 -->
			<view class="feedback-content">
				<view class="content-head">
					<p>意见说明</p>
					<text>{{content.length}}/200</text>
				</view>
				<textarea name="content" id="" cols="30" rows="10" maxlength="200" v-model="content"
				placeholder="请输入你的意见或建议使我们更好的服务" placeholder-style="color:rgba(191, 191, 191)"></textarea>
			</view>
			<!-- 上传凭证 -->
			<scroll-view class="upload-scroll" scroll-x enable-flex>
				<!-- 滑动查看照片 -->
				<view class="feedback-voucher">
					<view class="voucher-head">
						<p>上传凭证</p>
						<text>最多{{maxImage}}张</text>
					</view>
					<view class="upload-list">
						<view class="upload-item" @click="previewImage(index)" v-for="(item,index) in images" >
							<image :src="item" mode="aspectFill"></image>
							<!-- .stop阻止事件冒泡，触发父元素的图片预览事件-->
							<view class="del-btn" @click.stop="delectImage(index)">
								<uni-icons class="icon" type="closeempty" size="5" color="#fff"></uni-icons>
							</view>
						</view>
						<view class="upload-btn" @click="uploadImage" v-if="images.length < maxImage">
							<uni-icons type="plusempty" size="25" color="rgba(213, 213, 213)"></uni-icons>
							<text >上传照片</text>
						</view>
					</view>
				</view>
			</scroll-view>
			<!-- 提交 -->
			<button @click="handleSubmit" :class="{ 'active': isFormValid }" >提交</button>
		</view>
	</view>
</template>

<script setup>
import { ref,computed } from 'vue'

const gohome = () => {
	uni.reLaunch({
		url: '/pages/home/home'
	})
}
const gohistory = () => {
	uni.navigateTo({
	    url: '/pages/subpages/feedback/history'
	  })
}

const typeOptions = ref(['闪退', '页面错位', '页面卡顿', '吐槽产品功能', '其他问题反馈']);
const popup = ref(null);   // 获取 popup 组件实例
// 方法：打开选择器
const selectedType = ref('');	
const displayType = computed(() => selectedType.value || '样式问题');
/* const openTypePicker = () => {
	// 底部弹出式菜单 API
	uni.showActionSheet({
		// 可选的类型列表
		itemList: typeOptions,
		success:(res) => {
			selectedType.value = typeOptions[res.tapIndex]
		},
		fail:(err)=>{
			console.log('取消或失败', err)
		}
	})
	
} */
const openPopup = ()=> {
	// 判断弹窗存在,避免组件未加载好时被调用
	if(popup.value){
		// 调用弹窗打开方法
		popup.value.open()
	}
}
const closePopup = ()=> {
	if(popup.value){
		popup.value.close()
	}
}

//选择样式类型处理函数
const pickType = (item) => {
	selectedType.value = item;
	console.log('选中的值:', selectedType.value);
	closePopup()
}

//意见说明
const content = ref('');


//凭证上传
const maxImage = 6;
const images = ref([]);

//上传图片
const uploadImage = () => {
	uni.chooseImage({
		count:maxImage - images.value.length, //剩余可选数量
		sizeType:['compressed'],  //可指定原图（original）或压缩图（compressed）
		sourceType:['album','camera'], //从相册 相机选择
		success:(res) => {
			images.value.push(...res.tempFilePaths)
		},
		fail:(err) => {
			console.log('上传失败',err)
		}
	})
}

//预览图片
const previewImage = (index) => {
	uni.previewImage({
		current:images.value[index],
		urls:images.value
	})
}

//删除图片
const delectImage = (index) => {
	images.value.splice(index,1)  //splice(startindex,length)
}

const isSubmitted = ref(false);
//判断表单是否有效
const isFormValid = computed(() => {
	return selectedType.value !== '' &&
	       content.value !== ''  &&
		   images.value.length > 0
})
//判断内容是否填完 
const handleSubmit = () => {
	if(!isFormValid.value){
		return
	}
	
	//提交
	uni.showToast({title:'提交中...'})
	setTimeout(() => {
		uni.hideLoading()
		isSubmitted.value = true
	},1000)
	
}

</script>

<style lang="scss">
.feedback{
		background: rgba(248, 248, 248);
	.feedback-head{
		height: 120rpx;
		background-color: rgba(255, 255, 255);
		display:flex;
		justify-content: space-between;
		align-items: center;
		padding: 50rpx 10rpx 0 10rpx;
		h2{
			padding-left: 50rpx;
			font-weight: 550;
			font-size: 38rpx;
		}
		p{
			font-weight: 500rpx;
		}
	}
	// 反馈类型
	.feedback-type{
		height: 130rpx;
		margin: 20rpx;
		padding:0 20rpx;
		border-radius: 15rpx;
		background-color: rgba(255, 255, 255);
		display:flex;
		align-items: center;
		justify-content: space-between;
		p{
			font-weight: 600;
		}
	}
	// 弹窗选择器
	.popup-container{
		border-radius: 15rpx;
		padding: 20rpx 40rpx 80rpx 40rpx;
		background-color: #fff;
		p{
			height: 100rpx;
			text-align: center;
			justify-content: center;
			font-weight: 600;
		}
		.popup-list{
			display:flex;
			flex-direction: column;
			gap:40rpx;
		}
	}
	.feedback-content{
		height: 500rpx;
		background-color: rgba(255, 255, 255);
		margin: 20rpx;
		padding: 0 20rpx;
		border-radius: 15rpx;
		.content-head{
			padding:20rpx;
			display:flex;
			p{
				font-weight: 600;
			}
			text{
				font-size: 27rpx;
				padding: 10rpx 0 0 20rpx;
				color:rgba(191, 191, 191);
			}
		}
		textarea{
			padding:20rpx;
		}
	}
	//上传凭证
	.upload-scroll{
		width:100%;
		height: 300rpx;  // 必须固定高度，否则滚动无效
		white-space: nowrap;  // 强制一行显示
		.feedback-voucher{
			height: 300rpx;
			background-color: rgba(255, 255, 255);
			margin: 20rpx;
			padding: 0 20rpx;
			border-radius: 15rpx;
			.voucher-head{
				padding:20rpx;
				display:flex;
				p{
					font-weight: 600;
				}
				text{
					font-size: 27rpx;
					padding: 10rpx 0 0 20rpx;
					color:rgba(191, 191, 191);
				}
			}
			.upload-list{
				display: inline-flex;      /* 关键：让容器宽度由内容撑开 */
				flex-wrap: nowrap;         /* 禁止换行 */
				flex-direction: row;
				gap: 20rpx;
				.upload-item{
					position: relative;
					display:flex;
					overflow:hidden;
					// display:inline-flex; // 让宽度由内容撑开，而不是占满父级
					image{
						border-radius: 15rpx;
						height: 180rpx;
						width: 180rpx;
					}
					.del-btn{
						height: 40rpx;
						width: 40rpx;
						border-radius: 50%;
						background-color: rgba(29, 20, 28,0.5);
						position: absolute;
						z-index:99;
						top: -7rpx;          // 向上超出图片边缘
						right: -7rpx;        // 向右超出图片边缘
						display: flex;           
						align-items: center;   
						justify-content: center;
						.icon{
							
						}
					}
				}
				.upload-btn{
					height: 180rpx;
					width: 180rpx;
					background-color: rgba(248, 248, 248);
					border-radius: 17rpx;
					display: flex;
					flex-direction: column;   // 上下排列
					justify-content: center;
					align-items: center;
					text{
						color:rgba(220, 220, 220);
						font-size: 20rpx;
					}
				}
			}
		}
	}
	button{
		margin: 20rpx;
		background-color: rgba(148, 215, 209);
		color:white;
		&.active{
			background-color: rgba(0, 164, 149);
			color:white;
		}
		 // &:disabled{
			//  // background-color: white;
			//  background-color: rgba(148, 215, 209);
			//  color:white;
		 // }
	}
	.success-submit{
		background-color: rgba(248, 248, 248);
		display:flex;
		flex-direction: column;
		align-items: center;
		margin-top: 200rpx;
		height: 100vh;
		image{
			height: 300rpx;
			width: 300rpx;
		}
		p{
			padding-top: 30rpx;
			font-size: 35rpx;
			font-weight: 600;
		}
		span{
			font-size: 20rpx;
			padding-top: 30rpx;
			color:rgba(151, 151, 151);
			
		}
	}
}
</style>
