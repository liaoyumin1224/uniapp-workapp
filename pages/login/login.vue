<template>
	<view class="login">
		<view class="login-logo">
			<image src="/common/images/logo/logo.png"></image>
		</view>
		<view class="form-area">
			<!-- 手机号（带国家代码） -->
			<view class="filed filed-number">
				<view class="prefix" >+86</view>
				<input type="number" placeholder="请输入手机号码" v-model="phone" maxlength="11">
			</view>
			<!-- 验证码 + 获取验证码按钮 -->
			<view class="filed code-filed">
				<input type="number" placeholder="验证码" v-model="code">
				<button class="get-code-btn" @click="sendCode" 
				:disabled="codeSending">{{countdown > 0 ?`${countdown}s` : '获取验证码'}}</button>
			</view>
			<!-- 密码 -->
			<view class="filed filed-password">
				<input type="password" placeholder="请输入密码" v-model="password">
				<uni-icons type="eye-slash" size="30"></uni-icons>
			</view>
			<!-- 登录按钮 -->
			<view class="filde filed-login">
				<button class="login-btn" @click="handLogin" :class="{ 'active': isFormValid }" >登录</button>
			</view>
		</view>
		<view class="slogan">
			<span>坚守初心，铸就梦想</span>
		</view>
	</view>
</template>

<script setup>
import {computed, ref} from 'vue';

const phone = ref('');
const countdown = ref(0);//倒计时事件
const codeSending = ref(false);//防止重复点击
let timer = null;
const code = ref('');
const password = ref('');

// 发送验证码
const sendCode = ()=>{
	// .trim()移除字符串两端的空白字符（包括空格、制表符、换行符等）
	if(!phone.value.trim()){
		// 字符串去除空格等为空
		uni.showToast({title:'！请输入手机号' ,icon:'none'})
		return
	}
	if (phone.value.length !== 11) {
	    uni.showToast({ title: '！手机号格式错误', icon: 'none' })
	    return
	  }
	codeSending.value = true
	
	// 发送验证码
	setTimeout(()=>{
		countdown.value = 60;
		timer = setInterval(()=>{
			if(countdown.value > 1){
				countdown.value--;
			}else{
				// 清除定时器
				clearInterval(timer);
				countdown.value = 0;
				codeSending.value = false
			}
		},1000)
	},800)
}

/* 登录(登录处理)
1.验证码 手机号已经填写完毕
2.密码格式正确(8位以上数字/字母) */
//按钮颜色变换是根据内容实时变换的	
const isFormValid = computed(() => {
	return phone.value.length === 11 &&
	       code.value.trim() !== "" &&
		   password.value.length >= 8
})

// 提交时判断
const handLogin = ( ) =>{
	if(!phone.value.trim()){
		// 字符串去除空格等为空
		uni.showToast({title:'！请输入手机号' ,icon:'none'})
		return
	}
	if (!code.value.trim()) {
	    uni.showToast({ title: '！请输入验证码', icon: 'none' })
	    return
	  }
	if (!password.value.trim()) {
	    uni.showToast({ title: '！请输入密码', icon: 'none' })
	    return
	  }
	if (password.value.length < 8) {
	    uni.showToast({ title: '！请输入8位数字、字母', icon: 'none' })
	    return
	  }
	  if(!isFormValid.value){
		  return
	  }
	// 登录接口
	uni.showToast({title:'登录中...'})
		
	setTimeout(() => {
		uni.hideLoading();
		// 本地保存用户信息
		uni.setStorageSync('token','fake-token');
		uni.showToast({title:'登录成功',icon:'success'});
		setTimeout(() => {
			uni.reLaunch({url:'/pages/home/home'})
		},500)
	},1000)
}
</script>

<style lang="scss">
.login{
	.login-logo{
		height: 400rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		image{
			height: 180rpx;
			width: 180rpx;
		}
	}
	.form-area{
		// height: 380px;
		width: 630rpx;
		margin: 0 60rpx;
		align-items: center;
		.filed-number{
			display: flex;
			height: 95rpx;
			align-items: center;
			border-bottom: 1px solid rgba(240, 242, 245,0.5);
			.prefix{
				height: 100%;
				font-size: 16px;
				font-weight: 600;
				padding-right: 20rpx;
				line-height: 100rpx;     // 与父容器等高(垂直居中)
			}
			input{
				flex: 1;            // 占据剩余所有宽度
				min-width: 0;       // 防止溢出
			}
		}
			
		.code-filed{
			display: flex;
			justify-content: space-between; 
			align-items: center;
			height: 95rpx;
			border-bottom: 1px solid rgba(240, 242, 245,0.5);
			.get-code-btn{
				font-size: 14rpx;
				display: flex;
				align-items: center;      // 垂直居中
				justify-content: center;  // 水平居中
				height: 70rpx;
				width: 200rpx;
				background: rgba(255, 255, 255);
			}
		}
		.filed-password{
			display: flex;
			justify-content: space-between; 
			align-items: center;
			height: 95rpx;
			border-bottom: 1px solid rgba(240, 242, 245,0.5);
		}
		.filed-login{
			margin-top: 60rpx;
			.login-btn{
				background: rgba(153, 220, 213);
				color: rgba(232, 246, 249);
				&.active{
					background: rgba(0, 164, 149);
				}
			}
		}
	}
	.slogan{
		text-align: center;      // 水平居中
		width: 100%;             // 保证占满宽度
		padding-top: 400rpx;   // 可选，距离底部间距
		font-size: 28rpx;
		color: #666;
	}
}       
</style>
