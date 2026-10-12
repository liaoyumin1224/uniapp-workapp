<template>
	<!-- 历史建议界面 -->
	<view class="history-feedback">
		<!-- 历史建议头部 +日历-->
		<view class="history-head">
			<uni-icons class="icon" @click="gofeedback" type="left" size="25" weight="600" style="font-weight: bold;"></uni-icons>
			<h2>历史建议</h2>
			<!-- 点击图片打开日历 -->
			<image src="/common/images/pages/筛选@3x.jpg" @click="openPopup"></image>
			
		</view>
		<!-- 自定义日历弹窗 -->
		<uni-popup ref="popup" type="bottom" @maskClick="closePopup">
			<view class="popup-container">
				<!-- 标题 -->
				<view class="popup-title">时间筛选</view>
				<!-- 开始/结束时间 -->
				<view class="time">
					<view class="range">
						<view class="start">
							<text class="label">开始时间</text>
							<text class="value">{{ startDate || '请选择' }}</text>
						</view>
						<view class="end">
							<text class="label">结束时间</text>
							<text class="value">{{ endDate || '请选择' }}</text>
						</view>
					</view>
					<uni-calendar 
					          ref="calendar"
					          :insert="true"
					          :range="true"
					          :lunar="false"
					          @change="onCalendarChange"
					          @monthSwitch="onMonthSwitch"
					        />
				</view>
			</view>
			
		</uni-popup> 
		
		
		<!-- 筛选栏 -->
		<view class="history-tabs">
			<scroll-view class="scroll-tabs"scroll-x show-scrollbar="false"> 
				<view class="tabs-wrapper">
					<view class="tab-item" v-for="(tab,index) in tabs" :key="index" 
					@click="activeTab = tab" :class="{ active: activeTab === tab }">
						<text class="tabs-label">{{tab}}</text>
					</view>
				</view>
			</scroll-view>
		</view>
		<!-- 列表 -->
		<view class="history-list">
			<view class="list-item" v-for="(item,index) in filteredList" :key="index">
				<view class="list-head">
					<view class="left">
						<text class="status" :style="{ backgroundColor: statusColor(item.status) }">{{item.status}}</text>
						<text class="type">{{item.type}}</text>
					</view>
					<text class="time">{{item.time}}</text>
				</view>
				<text class="content">{{item.content}}</text>
			</view>
		</view>
	</view>
</template>

<script setup>
import {ref,computed} from 'vue'

const gofeedback = () => {
	uni.navigateTo({
		url: '/pages/subpages/feedback/feedback'
	})
}
//日历
//弹窗打开
const popup = ref(null)
const openPopup = () => {
	if(popup.value){
		//调用打开弹窗方法
		popup.value.open()
	}
}
const closePopup = () => {
	if(popup.value){
		//调用打开弹窗方法
		popup.value.close()
	}
}

//日历组件引用 
const calendar = ref(null)
// 选中日期
const startDate = ref(null)
const endDate = ref(null)
// 日历事件
const onCalendarChange = (e) => {
	console.log('日历change事件:', JSON.stringify(e))  // 👈 加上这行
	const before = e.range?.before   // 第一个选中的日期
	const after = e.range?.after     // 第二个选中的日期
  // 判断是否有 data 数组（说明已经选了两个日期）
    if (before && after) {
		if (before > after) {
		      // before 比 after 晚 → 交换
		      startDate.value = after
		      endDate.value = before
		    } else {
		      // before 比 after 早 → 正常赋值
		      startDate.value = before
		      endDate.value = after
		    }
      // 关闭弹窗
      setTimeout(() => {
        closePopup()
      }, 300)
    }
    // 如果只选了一个日期（before 有值，after 为空），可以只显示开始时间
    else if (e.range && e.range.before && !e.range.after) {
      startDate.value = e.range.before
      endDate.value = null   // 结束时间清空
    }
}

const onMonthSwitch = (e) => {
  console.log('月份切换:', e)
}

const tabs = ref(['全部', '待接收', '已接收', '已处理', '已拒绝'])
const activeTab = ref('全部')  // 当前选中的选项卡 key

const list = ref([
	{status:'已接收',type:'其他问题反馈',time:'2026-07-01',content:'食堂菜色一年没变，建议更换食堂菜色'},
	{status:'已排期',type:'吐槽产品功能',time:'2026-07-01',content:'功能开发太慢'},
	{status:'已处理',type:'吐槽产品功能',time:'2026-07-06',content:'字体不好看'},
	{status:'已拒绝',type:'其他问题反馈',time:'2026-07-06',content:'食堂菜色一年没变，建议更换食堂菜色'},
	{status:'待接收',type:'其他问题反馈',time:'2026-07-06',content:'食堂菜色一年没变，建议更换食堂菜色'}
])
// 筛选过滤列表
// ===============计算属性：根据 activeTab 过滤列表===================
const filteredList = computed(() => {
  // 1. 状态筛选
  let result = list.value
  if (activeTab.value !== '全部') {
    result = result.filter(item => item.status === activeTab.value)
  }

  // 2. 时间筛选
  const start = startDate.value
  const end = endDate.value
  if (start && end) {
    const startTime = new Date(start).getTime()
    const endTime = new Date(end).getTime()
    result = result.filter(item => {
      const itemTime = new Date(item.time).getTime()
      return itemTime >= startTime && itemTime <= endTime
    })
  } else if (start) {
    const startTime = new Date(start).getTime()
    result = result.filter(item => new Date(item.time).getTime() >= startTime)
  } else if (end) {
    const endTime = new Date(end).getTime()
    result = result.filter(item => new Date(item.time).getTime() <= endTime)
  }

  return result
})

// =====================状态颜色映射===================
const statusColor = (status) => {
	const colorMap = {
		'待接收': '#ffa500',   // 橙色
		'已接收': '#07c160',   // 绿色
		'已处理': '#1989fa',   // 蓝色
		'已拒绝': '#ee0a24',   // 红色
		'已排期': '#7232dd'    // 紫色
	}
	return colorMap[status] || 'red'
}

</script>

<style lang="scss">
page {
  background: rgba(248, 248, 248);
}
// 头部
.history-feedback{
	.history-head{
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
		image{
			padding-right: 20rpx;
			height: 35rpx;
			width: 35rpx;
		}
	}
}
// 自定义日历弹窗
.popup-container{
	padding: 30rpx 30rpx 20rpx;        // 内边距留白
	box-sizing: border-box;            // 保证 padding 不撑大宽度
	background-color: white;
	justify-content: center;
	align-content: center;
	border-radius: 15rpx;
	.popup-title{
		padding-top: 30rpx;
		height: 100rpx;
		text-align: center;
		justify-content: center;
		font-weight: 600;
		font-size: 35rpx;
	}
	.time{
		width: 100%;
		display:block;
		justify-content: space-between;
		align-items: center;
		.range{
			display: flex;
			flex-direction: row;         // 左右排列
			justify-content: space-between; // 开始靠左，结束靠右
			margin-bottom: 20rpx;
			padding: 0 60rpx;
			.start, .end {
			    display: flex;
			    flex-direction: column;      // label 在上，value 在下
			    align-items: flex-start;  // 左对齐，让文字左侧边缘齐平
			    gap: 6rpx;                  // label 和 value 之间的间距
			}
			.label {
			    font-weight: 600;
			    font-size: 26rpx;
			}
			
			.value {
			    font-size: 40rpx;
				font-weight: 600;
			}
		}
	}
}


// 筛选栏
.history-tabs{
	background-color: white;
	height: 100rpx;
	margin-bottom: 20rpx;
	.scroll-tabs{
		width: 100%;
		white-space:nowrap;
		.tabs-wrapper{
			display:inline-flex;//不换行
			.tab-item{
				padding: 24rpx 32rpx 7rpx 32rpx;
				position: relative;
				.tabs-label{
					font-size: 30rpx;
					font-weight: 600;
					color:rgba(125, 125, 125);
				}
				// 默认状态,下划线隐藏
				&::after{
					position: absolute;
					content:'';
					bottom:0;
					left: 50%;
					transform: translateX(-50%);
					width: 50rpx;
					height: 8rpx;
					border-radius: 10rpx;
					background-color: rgba(10, 158, 144);
					opacity:0;
					// ===== 添加过渡动画 =====
					transition: width 0.3s ease, opacity 0.3s ease;
				}
				// 选中状态
				&.active{
					.tabs-label {
					    color: rgba(24, 24, 24);
					  }
					// 下划线
					&::after{
						width: 50rpx;
						opacity: 1;   // 不透明
						
					}
				}
			}
		}
	}
}


// 列表
.history-list{
	background-color: rgba(248, 248, 248);
	.list-item{
		background-color: white;
		margin-bottom: 20rpx;
		padding: 40rpx 20rpx;
		.list-head{
			display: flex;
			align-items: center;
			justify-content: space-between;
			.left{
				align-items: center;
				.status{
					display: inline-flex;
					color: white;
					border-radius: 10rpx;
					font-size: 18rpx;
					padding: 3rpx 5rpx;
				}
				.type{
					padding-left: 20rpx;
					font-weight: 600;
					font-size: 30rpx;
				}
			}
			.time{
				color:rgba(223, 223, 223);
			}
		}
		.content{
			padding-top: 20rpx;
			display: block;
			color:rgba(119, 119, 119);
			font-size: 28rpx;
		}
	}
}
</style>