<template>
	<view>
		<view class="fr_top">
			<image class="fr_iamge" src="../../../../static/mark.png"></image>
			<view class="view_p">注：当保存参数后新参数即时生效。</view>
		</view>

		<!-- 结算价调整 -->
		<view class="body">
			<view class="view_h1"><view class="text">结算价参数</view></view>
			<view class="rule"></view>
			<!-- 信用卡 -->
			<block v-for="(item, index) in tradePrice.list" :key="index">
				<view class="rule-view">
					<view class="rule_p">
						<view class="view_p2">{{item.name}}</view>
						<input class="rule_input" v-model="item.rate * 0.01" disabled="true" />%
						<view class="view_s">{{item.min * 0.01}}% ~ {{item.max * 0.01}}%</view>
					</view>
				</view>
				<view class="rule"></view>
			</block>
		</view>
		<!-- 返现设置 -->
		<view class="body2">
			<view class="view_h1"><view class="text">返现参数（元）</view></view>
			<view class="rule"></view>
			<view class="rule-view">
				<view class="rule_p">
					<view class="view_p2">激活返现</view>
					<input class="rule_input2" v-model="activePrice.money" disabled="true" />
				</view>
			</view>
			<view class="rule"></view>
		</view>
	</view>
</template>

<script>
import net from '../../../../common/net.js';
export default {
	data() {
		return {
			pid: '',			// 政策id
			uid: '',			// 用户id
			
			tradePrice: [],		// 结算价参数
			activePrice: [],	// 返现参数
		};
	},
	
	onLoad(options) {
		this.pid = options.pid;
		this.uid = options.uid;
		// 获取用户政策信息
		this.getPolicyInfo();
	},
	
	methods: {
		// 获取用户政策信息
		getPolicyInfo(){
			net({
	        	url:"/V1/getPolicyInfo",
	            method:'POST',
				data:{
					uid: this.uid,
					pid: this.pid
				},
	            success: (res) => {
					this.tradePrice = res.data.success.data.trade_price;
					this.activePrice = res.data.success.data.active_price;
	            },
				fail: () => {
					uni.showToast({
						title: '政策信息获取失败',
						icon: 'none'
					})
				},
	      	})
		}
	}
};
</script>

<style>
@import url("../../../shouye/style/profit_data.css");
</style>
