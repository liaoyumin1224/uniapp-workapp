<template>
	<view class="oa-home">
		<!-- 滚动内容区（不包含底部栏） -->
		<scroll-view class="content-scroll" scroll-y>
			<!-- 消息页面 -->
			<view v-if="activeTab === '消息'">
				<!-- 消息头部 -->
				<view class="news-head">
					<view class="head">
						<h3>消息</h3>
						<image src="/common/images/home/news/clean@3x.jpg"></image>
					</view>
					<view class="search-container">
						<image src="/common/images/home/news/圆角矩形 2@3x.jpg"></image>
						<input type="text" placeholder="请输入消息标题搜索" placeholder-style="color:rgba(208, 208, 208)">
					</view>
				</view>
				<!-- 功能栏 -->
				<view class="news-func">
					<view class="newfunc-grid">
						<view class="newfunc-item" v-for="(item,id) in newfunc" :key="id"> 
							<image class="icon" :src="item.icon"></image>
							<p class="name">{{item.name}}</p>
							<span class="content">{{item.content}}</span>
						</view>
					</view>
				</view>
				<!-- 今日提醒 -->
				<view class="news-remind">
					<p class="todo-remind">今日提醒</p>
					<view class="remind-container">
						<view class="remind-grid">
							<view class="remind-item" v-for="(item,index) in remind" :key="index">
								<image :src="item.icon"></image>
								<view class="remind">
									<view class="title">
										<p class="name">{{item.name}}</p>
										<p class="time">{{item.time}}</p>
									</view>
									<span class="content">{{item.content}}</span>
								</view>
							</view>
						</view>
					</view>
				</view>
				<!-- 最近联系人 -->
				<view class="recent-contact">
					<p class="recent-person">最近联系人</p>
					<view class="recent-container">
						<view class="contact-grid">
							<view class="contact-item" v-for="(item,index) in contact" :key="index">
								<!-- 可滑动的内容区域 -->
								 <!-- transform 只接受 px 或百分比，所以这里必须将 rpx 转换为 px -->
								<view class="scorll-area" 
								:style="{transform: `translateX(${slideStates[index] || 0}px)`}"
								 @touchstart="handleTouchStart($event, index)"
							     @touchmove="handleTouchMove($event, index)"
								 @touchend="handleTouchEnd($event, index)">
									<image :src="item.icon"></image>
									<view class="contact">
										<view class="title">
											<p class="name">{{item.name}}</p>
											<p class="time">{{item.time}}</p>
										</view>
										<span class="content">{{item.content}}</span>
									</view>
								</view>
								<!-- 删除联系人按钮 -->
								<view class="del-btn" @click.stop="deleteContact(index)">删除</view>
							</view>
						</view>
					</view>
				</view>
			</view>

			<!-- 工作页面（首页） -->
			<view v-else-if="activeTab === '工作'">
				<!-- 公司信息卡片 -->
				<view class="header">
					<view class="header-head">
						<view class="company">重庆东实纸业有限公司</view>
						<uni-icons type="gear" size="30" color="white" class="icon"></uni-icons>
					</view>
					<view class="user-card">
						<view class="user-message">
							<image class="avatar" src="/common/images/user/avatar.jpg"></image>
							<view class="user-info">
								<text class="name">暖暖</text>
								<view class="info-row">
									<text class="dept">部门：革新委</text>
									<text class="position">职位：销售代表</text>
								</view>
							</view>
						</view>
						<view class="stats-grid">
							<view class="stat-card" v-for="(item, idx) in stats" :key="idx">
								<text class="num">{{ item.num }}</text>
								<text class="label">{{ item.label }}</text>
							</view>
						</view>
					</view>
				</view>

				<!-- 轮播图 -->
				<view class="banner">
					<swiper indicator-dots="true" autoplay="true" class="swiper-box">
						<swiper-item v-for="(item, idx) in 3" :key="idx">
							<image src="/common/images/home/2@3x(1).jpg" mode="aspectFill"></image>
						</swiper-item>
					</swiper>
				</view>

				<!-- 常用功能 -->
				<view class="section-title">
					<p>常用功能</p>
					<view class="func-grid">
						<view class="func-item" v-for="(item, idx) in func" :key="idx" @click="gotoPage(item)">
							<image class="icon" :src="item.icon"></image>
							<text class="name">{{ item.name }}</text>
						</view>
					</view>
				</view>

				<!-- 今日待办 -->
				<view class="section-title">
					<p>今日待办</p>
					<view class="todo-list">
						<view class="todo-item" v-for="(todo, idx) in todos" :key="idx">
							<image src="/common/images/home/组 3@3x(3).jpg"></image>
							<view class="todo-info">
								<view class="todo-title">{{ todo.title }}</view>
								<view class="todo-desc">{{ todo.desc }}</view>
							</view>
						</view>
					</view>
				</view>
			</view>

			<!-- 通讯录页面 -->
			<view v-else-if="activeTab === '通讯录'">
				<view class="contacts-placeholder">通讯录页面开发中</view>
			</view>
		</scroll-view>

		<!-- 底部导航栏（固定，在 scroll-view 外面） -->
		<view class="footer">
			<view class="footer-item" v-for="(footer, idx) in footers" :key="idx" @click="activeTab = footer.title">
				<view class="footer-info">
					<image class="icon" :src="activeTab === footer.title ? footer.activeIcon : footer.icon"></image>
					<view class="footer-title" :class="{ 'active': activeTab === footer.title }">{{ footer.title }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>
import { onMounted,ref,nextTick} from 'vue'

const activeTab = ref('工作')   // 默认显示工作台
//功能模块
const newfunc = ref([
	{id:'0',name:"提醒告警",icon:'../../common/images/home/news/1@3x(3).jpg'},
	{id:'1',name:"通知公告",icon:'../../common/images/home/news/1@3x(3).jpg'},
	{id:'2',name:"互动聊天",icon:'../../common/images/home/news/1@3x(3).jpg'}
])
//今日提醒模块
const remind = ref([
	{icon:'../../common/images/home/news/1@3x.jpg',name:'待办事务提醒',content:'今日13:11即将开会，收到请准备',time:'12:00'},
	{icon:'../../common/images/home/news/2@3x.jpg',name:'生日提醒',content:'张大伟、冯小兵今天生日，全司住他们生日快乐',time:'12:00'},
	{icon:'../../common/images/home/news/1@3x(1).jpg',name:'转正提醒',content:'今日13:11即将开会，收到请准备',time:'12:00'},
	{icon:'../../common/images/home/news/2@3x(1).jpg',name:'系统消息',content:'张大伟、冯小兵今天生日，全司住他们生日快乐',time:'12:00'},
	{icon:'../../common/images/home/news/1@3x(2).jpg',name:'通知公告',content:'今日13:11即将开会，收到请准备',time:'12:00'}
])

// 最近联系人模块
const contact = ref([
	{icon:'../../common/images/user/avatar.jpg',name:'张杉杉',content:'收到。',time:'12:00'},
	{icon:'../../common/images/user/avatar.jpg',name:'杜军',content:'好的，昨天说的不要忘记了！！',time:'12:00'},
	{icon:'../../common/images/user/avatar.jpg',name:'高小婷',content:'收到。',time:'12:00'},
	{icon:'../../common/images/user/avatar.jpg',name:'弓长',content:'好的，昨天说的不要忘记了！！',time:'12:00'}
])
 /* 滑动删除联系人
 偏移只接受px或百分比的写法,所以需要每次重新获取按钮大偏移量 */
 
const deleteBtnWidth = ref(0) // 获取删除按钮大小
const slideStates = ref([]) //获取每个联系人的偏移量（每个联系人是独立的）
let startX = 0 //触摸坐标

//获取删除按钮的宽度
const getDeleteBtnWidth = () => {
	
	 if (contact.value.length === 0) return
	 
	nextTick( () => {
		// 1. 创建一个查询器实例
		const query = uni.createSelectorQuery()
		// 2. 选择第一个 class 名为 'delete-btn' 的元素
		  query.select('.del-btn').boundingClientRect(data => {
			  console.log('查询结果:', data)
			   // 3. 如果成功获取到该元素的信息
		    if (data && data.width) {
			// 4. 将获取到的宽度（单位 px）保存到变量 deleteBtnWidth.value 中
		      deleteBtnWidth.value = data.width
		      // 5. 重新初始化所有滑动项的状态（因为宽度变了，滑动距离需要重新计算）
		      initSlideStates()
		    }//else{
			// 	// 不要回退到屏幕宽度！可以设定一个默认值或者重试
			// 	console.warn('获取宽度失败，稍后重试')
			// 	setTimeout(() => getDeleteBtnWidth(), 100) // 重试一次
			// }
		  }).exec() // 6. 执行查询
	})
	
}


// 触屏起始位置
const handleTouchStart = (e,index) => {
	// 记录手指按下时的视口 X 坐标
	 startX = e.touches[0].clientX    //touches[0]为触屏列表的第一个   clientX = 手指或鼠标距离视口左边界的距离
}
//触屏移动
const handleTouchMove = (e,index) => {
	//先判断宽度是否拿到
	if(!deleteBtnWidth.value) return
	console.log(deleteBtnWidth.value)
	//记录移动后的触屏位置
	const moveX = e.touches[0].clientX
	let diff = moveX - startX
	
	//限制偏移范围[-deleteBtnWidth,0]
	let newOffset = Math.max(-deleteBtnWidth.value,Math.min(0,diff)) //Math.min(0, diff)只允许左滑，禁止右滑露出空白
	slideStates.value[index] = newOffset	
	
}
// 偏移量结果判断
const handleTouchEnd = (e,index) => {
	if(!deleteBtnWidth.value) return
	
	const currentOffset = slideStates.value[index] //现在的偏移量
	const halfWidth = deleteBtnWidth.value / 2   // 改用绝对值比较
	  if (Math.abs(currentOffset) > halfWidth) {
	    slideStates.value[index] = -deleteBtnWidth.value
	  } else {
	    slideStates.value[index] = 0
	  }
}
//删除联系人处理
const deleteContact = (index) => {
	//删除联系人
	contact.value.splice(index,1)
	// 初始化偏移量
	initSlideStates()
}
// 初始化偏移量
const initSlideStates = () => {
  slideStates.value = new Array(contact.value.length).fill(0)
}

onMounted(() => {
//让代码等到 Vue 完成当前所有 DOM 更新之后再执行获取宽度
  nextTick(() => {
    getDeleteBtnWidth()
  })
})

// 统计数据
const stats = ref([
	{ num: '0', label: '待办事项' },
	{ num: '99', label: '待我审批' },
	{ num: '0', label: '未读消息' },
	{ num: '49', label: '未读公告' }
])

// 常用功能
const func = ref([
	{ name: '意见反馈', icon: '/common/images/home/1@3x(1).jpg' },
	{ name: '工资明细', icon: '/common/images/home/2@3x.jpg' },
	{ name: '预约就餐', icon: '/common/images/home/3@3x(1).jpg' },
	{ name: '全部', icon: '/common/images/home/4@3x(1).jpg' },
	{ name: '日周计划', icon: '/common/images/home/1@3x(2).jpg' },
	{ name: '心得体会', icon: '/common/images/home/3@3x(2).jpg' },
	{ name: '我的会议', icon: '/common/images/home/组 3@3x.jpg' },
	{ name: '物流订单', icon: '/common/images/home/组 3@3x(4).jpg' }
])

// 今日待办
const todos = ref([
	{ title: '待审批：工资异议', desc: '3月工资，张伟提交申请' },
	{ title: '待审批：工资异议', desc: '3月工资，张伟提交申请' },
	{ title: '待审批：工资异议', desc: '3月工资，张伟提交申请' }
])

// 底部导航数据
const footers = ref([
	{ icon: '/common/images/home/消息_面@3x.jpg', title: '消息', activeIcon: '/common/images/home/消息_面@3x(1).jpg' },
	{ icon: '/common/images/home/工作@3x.jpg', title: '工作', activeIcon: '/common/images/home/工作@3x(1).jpg' },
	{ icon: '/common/images/home/通讯录_面@3x.jpg', title: '通讯录', activeIcon: '/common/images/home/通讯录_面@3x(1).jpg' }
])

// 跳转到功能页
const gotoPage = (item) => {
	const pageMap = {
		'意见反馈': '/pages/subpages/feedback/feedback',
		'工资明细': '/pages/subpages/salary/salary',
		'预约就餐': '/pages/subpages/meal/meal',
		'全部': '/pages/subpages/all/all',
		'日周计划': '/pages/subpages/plan/plan',
		'心得体会': '/pages/subpages/insight/insight',
		'我的会议': '/pages/subpages/meeting/meeting',
		'物流订单': '/pages/subpages/logistics/logistics'
	}
	const url = pageMap[item.name]
	if (url) {
		uni.navigateTo({ url })
	} else {
		uni.showToast({ title: '页面开发中', icon: 'none' })
	}
}
</script>

<style lang="scss">
page, uni-page-body {
	height: 100%;
}

.oa-home {
	display: flex;
	flex-direction: column;
	height: 100vh;
	background: #f3f4f6;
}

.content-scroll {
	flex: 1;
	overflow-y: auto;
}

.footer {
	flex-shrink: 0;
	height: 100rpx;
	padding: 10rpx 0;
	background-color: #fff;
	display: flex;
	justify-content: space-around;
	align-items: center;
	border-top: 1px solid #eee;
}

.footer-item {
	.footer-info {
		display: flex;
		flex-direction: column;
		align-items: center;
		image {
			height: 48rpx;
			width: 48rpx;
		}
		.footer-title {
			font-size: 24rpx;
			color: #666;
			&.active {
				color: #07c160;
				font-weight: 500;
			}
		}
	}
}
/* ==================================================消息界面======================================== */
	
.news-head{
	height: 260rpx;
	background-color: white;
	margin-bottom: 20rpx;
	padding-top: 50rpx;
	.head{
		padding: 70rpx 0 30rpx 20rpx;
		display:flex;
		text-align: left;
		h3{
			font-size: 45rpx;
		}
		image{
			left:10rpx;
			top:20rpx;
			height: 30rpx;
			width: 30rpx;
		}
	}
	.search-container{
		position: relative;
		image{
			position: absolute;
			top:29rpx;
			left:50rpx;
			height: 30rpx;
			width: 30rpx;;
		}
		input{
			margin: 0 20rpx;
			padding: 19rpx 0 19rpx 80rpx;
			border-radius: 40rpx;
			background-color: rgba(246, 246, 246);
		}
	}
}
.news-func{
	background-color: white;
	margin: 0 20rpx;
	padding: 40rpx 0;
	border-radius: 20rpx;
	.newfunc-grid{
		display:flex;
		justify-content: space-around;
		.newfunc-item{
			.icon{
				height: 110rpx;
				width: 110rpx;
			}
		}
	}
}

.news-remind{
	background-color: white;
	margin: 20rpx 20rpx;
	border-radius: 20rpx;
	padding:0 20rpx;
	.todo-remind{
		padding: 30rpx 0;
		font-size: 30rpx;
		font-weight: 550;
	}
	.remind-container{
		.remind-grid{
			.remind-item{
				width: 100%;          // 确保父容器宽度受限
				display:flex;
				padding: 20rpx 0;
				border-bottom:  3px solid rgba(248, 248, 248);
				image{
					flex-shrink: 0;  // 防止图片被压缩
					height: 80rpx;
					width: 80rpx;
				}
				.remind{
					flex: 1;
					min-width: 0;
					padding-left: 20rpx;
					.title{
						display:flex;
						justify-content: space-between;
						.name{
							font-size: 30rpx;
							font-weight: 545;
						}
						.time{
							font-size: 17rpx;
							color:rgba(203, 203, 203);
						}
						
					}
					.content{
						display: block;          /* 关键！ */
						width: 100%;
						padding-top: 10rpx;
						color:rgba(150, 150, 150);
						white-space: nowrap;    /* 强制一行显示 */
						overflow: hidden;       /* 超出隐藏 */
						text-overflow: ellipsis;/* 超出显示省略号 */
					}
					
				}
			}
		}
	}
}

// 最近联系人
.recent-contact{
	background-color: white;
	margin: 20rpx 20rpx;
	border-radius: 20rpx;
	padding:0 20rpx;
	.recent-person{
		padding: 30rpx 0;
		font-size: 30rpx;
		font-weight: 550;
	}
	.recent-container{
		.contact-grid{
			.contact-item{
				// width: 100%;          // 确保父容器宽度受限
				// display:flex;
				// padding: 20rpx 0;
				// border-bottom:  3px solid rgba(248, 248, 248);
				position: relative;
				overflow: hidden;
				.scorll-area{
					position: relative;
					background-color: #fff; 
					width: 100%;          // 确保父容器宽度受限
					display:flex;
					padding: 20rpx 0;
					border-bottom:  3px solid rgba(248, 248, 248);
					// overflow: hidden;
					z-index:2;
					image{
						flex-shrink: 0;  // 防止图片被压缩
						height: 80rpx;
						width: 80rpx;
						border-radius: 50%;
					}
					.contact{
						flex: 1;
						min-width: 0;
						padding-left: 20rpx;
						.title{
							display:flex;
							justify-content: space-between;
							.name{
								font-size: 30rpx;
								font-weight: 545;
							}
							.time{
								font-size: 17rpx;
								color:rgba(203, 203, 203);
							}
							
						}
						.content{
							display: block;          /* 关键！ */
							width: 100%;
							padding-top: 10rpx;
							color:rgba(150, 150, 150);
							white-space: nowrap;    /* 强制一行显示 */
							overflow: hidden;       /* 超出隐藏 */
							text-overflow: ellipsis;/* 超出显示省略号 */
						}
						
					}
				}
				.del-btn{
					display: flex;
					right: 0;            // 紧贴父容器右侧
					top: 0;
					bottom: 0;  
					width: 160rpx;
					position: absolute;
					background-color: red;
					z-index:1;
				}
			}
		}
	}
}

/* ======================================== 工作====================================================== */
.header {
	height: 500rpx;
	position: relative;
	.header-head {
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0 20rpx;
		height: 300rpx;
		background-image: url("/common/images/home/bj@3x.jpg");
		background-size: cover;
		background-position: center;
		.company {
			font-size: 50rpx;
			font-weight: 500;
			color: white;
			flex: 1;
			min-width: 0;
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
		}
	}
	.user-card {
		position: absolute;
		top: 220rpx;
		left: 50%;
		width: 87%;
		transform: translateX(-50%);
		background: #fff;
		padding: 0 30rpx;
		border-radius: 20rpx 20rpx 0 0;
		.user-message {
			display: flex;
			padding-top: 20rpx;
			image {
				border-radius: 50%;
				height: 120rpx;
				width: 120rpx;
			}
			.user-info {
				display: flex;
				flex-direction: column;
				padding-left: 30rpx;
				.name {
					margin-top: 10rpx;
					font-size: 40rpx;
					font-weight: 550;
				}
				.info-row {
					margin-top: 10rpx;
					font-size: 26rpx;
					.dept::after {
						content: "|";
						margin: 0 20rpx;
					}
				}
			}
		}
		.stats-grid {
			margin-top: 20rpx;
			display: flex;
			justify-content: space-around;
			margin-bottom: 20rpx;
			.stat-card {
				display: flex;
				flex-direction: column;
				align-items: center;
				.num {
					font-size: 40rpx;
					font-weight: bold;
				}
				.label {
					font-size: 24rpx;
					margin-top: 8rpx;
				}
			}
		}
	}
}

.banner {
	padding: 0 20rpx;
	margin-top: 20rpx;
	.swiper-box {
		height: 300rpx;
		border-radius: 15rpx;
		overflow: hidden;
	}
	image {
		height: 100%;
		width: 100%;
	}
}

.section-title {
	margin: 30rpx 20rpx;
	background: #fff;
	border-radius: 15rpx;
	padding-bottom: 20rpx;
	p {
		font-size: 28rpx;
		padding: 20rpx 0 0 30rpx;
		color: #666;
		font-weight: 500;
	}
	.func-grid {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		padding: 30rpx 20rpx;
		gap: 40rpx;
		.func-item {
			display: flex;
			flex-direction: column;
			align-items: center;
			.icon {
				width: 80rpx;
				height: 80rpx;
			}
			.name {
				font-size: 24rpx;
				margin-top: 10rpx;
			}
		}
	}
	.todo-list {
		margin: 0 20rpx;
		.todo-item {
			display: flex;
			padding: 20rpx 0;
			border-bottom: 1px solid #f0f0f0;
			image {
				width: 48rpx;
				height: 48rpx;
				margin-right: 20rpx;
			}
			.todo-info {
				flex: 1;
				.todo-title {
					font-size: 28rpx;
					font-weight: 500;
				}
				.todo-desc {
					font-size: 24rpx;
					color: #999;
					margin-top: 8rpx;
				}
			}
		}
	}
}

</style>