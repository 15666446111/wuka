<template>
	<view class="background">
		<navigator url="tijiaoyinhangka/tijiaoyinhangka?pages=cash">
			<view class="top">
				<!-- <image src="/static/minsheng.png" class="img"></image> -->
				<view class="fview">
					<view style="font-size: 30rpx;">{{ bankCard.bank_name }}</view>
					<view style="font-size: 30rpx;">{{ bankCard.bank }}</view>
				</view>
				<image src="/static/jiantou.png" class="cxk"></image>
			</view>
		</navigator>
		<view class="body">
			<view class="select-view tab">
				<view class="select-text" @tap="toggleTab('selector')">{{ resultInfo.result }}</view>
				<w-picker mode="selector" @confirm="onConfirm" ref="selector" themeColor="#f00" :selectList="selectList"></w-picker>
				<image class="select-image" src="/static/EPOS/jiantou.png" />
			</view>
			<view class="bview">
				<text>可提现金额：</text>
				<text style="color: #EE9900;">{{balance / 100 }}</text>
				<text>元</text>
			</view>
			<view class="hengxian"></view>
			<view class="bview d-flex">
				<input class="qbtxje" placeholder="请输入提现金额" @input="moneyInput" v-model="money" />
				<view style="color:#f0ad4e; font-size: 30upx; height: 100upx; line-height: 100upx;" @click="cashAll">全部提现</view>
			</view>
			<view class="hengxian"></view>
			<view class="bview">
				<text>应到金额：</text>
				<text style="color:#f0ad4e;">{{ reachBalance / 100 }}</text>
				<text style="margin-left:10upx;">元</text>
			</view>
			<view class="hengxian"></view>
			<view class="bview4">
				<view>提示：手续费 {{cashsetUp.rate_m}} 元，税点 {{ cashsetUp.point }} %，单笔提现金额不低于 {{ cashsetUp.min_money }} 元</view>
				<view>提现时间： {{ cashsetUp.point_time }} ,请注意查收短信或查询提现进度</view>
			</view>
		</view>
		
		<view class="cash-list">
			<navigator url="../tixianjilu/tixianjilu">
				<view class="list-li">
					<image src="../../../static/new/hbjl.png" class="left-img" mode="widthFix"></image>
					<view class="list-title">提现记录</view>
					<image src="../../../static/EPOS/jiantou.png" class="right-arrow" mode="widthFix"></image>
				</view>
			</navigator>
		</view>

		<button class="button" @click="setWithdrawal">立即提现</button>
	</view>
</template>

<script>
import net from '../../../common/net.js';

import wPicker from '@/components/w-picker/w-picker.vue';
let _this = null;
export default {
	components: {
		wPicker
	},
	data() {
		return {
			title: 'Hello',
			startYear: new Date().getFullYear(),
			selectList: [
				{
					label: '分润钱包',
					value: 1
				},
				{
					label: '返现钱包',
					value: 2
				}
			],
			resultInfo: {
				result: '请选择提现钱包',
				value: ''
			},
			// 用户信息
			UserInfo: [],
			// 提现设置信息
			cashsetUp: [],
			// 结算卡信息
			bankCard: {
				bank_name: '请选择提现银行卡'
			},
			bank_name: '',
			// 可提现金额
			balance: '',
			// 应到账金额
			reachBalance: '',
			// 提现金额
			money: '',
		};
	},
	
	onLoad() {
		// 获取用户个人信息
		this.getUserInfo();
		// 获取默认结算卡信息
		this.getDefaultCard();
	},
	
	methods: {
		toggleTab(str) {
			this.$refs[str].show();
		},
		onConfirm(val) {
			this.resultInfo = { ...val };
			this.balance = val.checkArr.value == 1 ? this.UserInfo.cash_blance : this.UserInfo.return_blance;
			
			// 获取提现税点设置信息
			this.getPoint(this.resultInfo.checkArr.value);
		},
		
		// 获取用户个人信息
		getUserInfo(){
			net({
	        	url:"/V1/mine",
	            method:'get',
	            success: (res) => {
					this.UserInfo = res.data.success.data;
	            }
	      	})
		},
		
		// 获取提现税点设置信息
		getPoint(type){
			net({
	        	url:"/V1/getPoint",
	            method:'get',
				data: {
					'type' : type,
				},
	            success: (res) => {
					// console.log(res);
					this.cashsetUp = res.data.success.data;
	            }
	      	})
		},
		
		// 获取默认结算卡信息
		getDefaultCard(){
			net({
	        	url:"/V1/getBankDefault",
	            method:'get',
	            success: (res) => {
					if (res.data.success.data != null) {
						this.bankCard = res.data.success.data;
					}
	            }
	      	})
		},
		
		// 发起提现
		setWithdrawal(){
			// console.log(this.resultInfo.checkArr.value);
			if (this.resultInfo.checkArr == undefined) {
				uni.showToast({ title: '请选择提现钱包', icon: 'none' })
				return false;
			}
			// 加载动画
			uni.showLoading();
			net({
	        	url: "/V1/getWithdrawal",
	            method: 'POST',
				data: {
					'money': this.money * 100,
					'blance': this.resultInfo.checkArr.value,
					'rate': this.cashsetUp.point * 0.01,
					'rate_m': this.cashsetUp.point.rate_m,
					'remark': '',
					'bank': this.bankCard.bank,
					'name': this.bankCard.name,
					'number': this.bankCard.number,
					'open_bank': this.bankCard.open_bank,
					'bank_name': this.bankCard.bank_name,
				},
	            success: (res) => {
					console.log(res);
					uni.hideLoading();
					if (res.data.success) {
						uni.showToast({
							title: '提交成功',
							icon: 'none',
							success: (res) => {
								this.balance = this.balance - this.money * 100;
							}
						})
					} else {
						uni.showToast({
							title: res.data.error.message,
							icon: 'none'
						})
					}
	            }
	      	})
		},
		
		// 同步应提现金额方法
		moneyInput(event){
			var eventMoney = event.detail.value;
			var settleMoney = eventMoney * (1 - this.cashsetUp.point * 0.01);
			this.reachBalance = Math.floor(settleMoney * 100);
		},
		
		// 全部提现
		cashAll(){
			if (this.resultInfo.checkArr == undefined) {
				uni.showToast({ title: '请选择提现钱包', icon: 'none' })
				return false;
			}
			this.money = this.balance / 100;
			var settleMoney = this.money * (1 - this.cashsetUp.point * 0.01);
			console.log(settleMoney);
			this.reachBalance = Math.floor(settleMoney * 100);
		}
	}
};
</script>

<style>
	@import url("../style/cash-out.css");
</style>
