<template>
	<view class="wrap">
		<u-form :model="model" :rules="rules" ref="uForm" :errorType="errorType">
			<u-form-item :label-position="labelPosition" label="进货日期" prop="region" label-width="150">
				<u-input :border="border" type="select" :select-open="pickerShow" v-model="model.region" placeholder="请选择日期" @click="pickerShow = true"></u-input>
			</u-form-item>

			<u-form-item :label-position="labelPosition" label="供应商" prop="goodsType" label-width="150">
				<u-input :border="border" type="select" :select-open="selectShow" v-model="model.goodsType" placeholder="请选择供应商" @click="selectShow = true"></u-input>
				<u-button slot="right" type="success" size="mini" @click="getCode">{{codeTips}}</u-button>
			</u-form-item>
			<u-form-item :label-position="labelPosition" label="支付方式" prop="sex" label-width="150">
				<u-input :border="border" type="select" :select-open="actionSheetShow" v-model="model.sex" placeholder="请选择支付方式" @click="actionSheetShow = true"></u-input>
			</u-form-item>

			<u-form-item :label-position="labelPosition" label="进货凭证" prop="photo" label-width="150">
				<u-upload width="160" height="160"></u-upload>
			</u-form-item>
			<u-form-item label-width="150" :label-position="labelPosition" label="进货商品" prop="name">
				<u-input :border="border" placeholder="请输入商品名称快速选择" v-model="model.name" type="text"></u-input>
			</u-form-item>
		</u-form>

		<u-button @click="submit">添加商品</u-button>
		<u-action-sheet :list="actionSheetList" v-model="actionSheetShow" @click="actionSheetCallback"></u-action-sheet>
		<u-select mode="single-column" :list="selectList" v-model="selectShow" @confirm="selectConfirm"></u-select>
		<u-picker mode="region" v-model="pickerShow" @confirm="regionConfirm"></u-picker>

	</view>
</template>

<script>
export default {
	data() {
		let that = this;
		return {
			model: {
				name: '',
				sex: '',
				likeFruit: '',
				intro: '',
				payType: '支付宝',
				agreement: false,
				region: '',
				goodsType: '',
				phone: '',
				code: '',
				password: '',
				rePassword: '',
				remember: false,
				photo: ''
			},
			selectList: [
				{
					value: '电子产品',
					label: '电子产品'
				},
				{
					value: '服装',
					label: '服装'
				},
				{
					value: '工艺品',
					label: '工艺品'
				}
			],
			rules: {
				
				sex: [
					{
						required: true,
						message: '请选择性别',
						trigger: 'change'
					},
				],
				
				payType: [
					{
						required: true,
						message: '请选择任意一种支付方式',
						trigger: 'change',
					}
				],
				goodsType: [
					{
						required: true,
						message: '请选择商品类型',
						trigger: 'change',
					}
				],
				code: [
					{
						required: true,
						message: '请输入验证码',
						trigger: ['change','blur'],
					},
					{
						type: 'number',
						message: '验证码只能为数字',
						trigger: ['change','blur'],
					}
				],
			},
			border: false,
			check: false,
			selectStatus: 'close',
		
			radioList: [
				{
					name: '支付宝',
					checked: true,
					disabled: false
				},
				{
					name: '微信',
					checked: false,
					disabled: false
				},
				{
					name: '银联',
					checked: false,
					disabled: false
				},
				{
					name: '现金',
					checked: false,
					disabled: false
				}
			],
			radio: '支付宝',
			actionSheetList: [
				{
					text: '男'
				},
				{
					text: '女'
				},
				{
					text: '保密'
				}
			],
			actionSheetShow: false,
			pickerShow: false,
			selectShow: false,
			radioCheckWidth: 'auto',
			radioCheckWrap: false,
			labelPosition: 'left',
			codeTips: '',
			errorType: ['message'],
		};
	},
	onLoad() {

	},
	computed: {
		borderCurrent() {
			return this.border ? 0 : 1;
		}
	},
	onReady() {
		this.$refs.uForm.setRules(this.rules);
	},
	methods: {
		submit() {
			this.$u.route({
				url: 'pages/goodsAdd/index',
				params: {
					name: 'lisa'
				}
			})
			// this.$refs.uForm.validate(valid => {
			// 	if (valid) {
			// 		if(!this.model.agreement) return this.$u.toast('请勾选协议');
			// 		console.log('验证通过');
			// 	} else {
			// 		console.log('验证失败');
			// 	}
			// });
		},
		// 点击actionSheet回调
		actionSheetCallback(index) {
			uni.hideKeyboard();
			this.model.sex = this.actionSheetList[index].text;
		},
		// checkbox选择发生变化
		checkboxGroupChange(e) {
			this.model.likeFruit = e;
		},
		// radio选择发生变化
		radioGroupChange(e) {
			this.model.payType = e;
		},
		
		// 选择地区回调
		regionConfirm(e) {
			this.model.region = e.province.label + '-' + e.city.label + '-' + e.area.label;
		},
		// 选择商品类型回调
		selectConfirm(e) {
			this.model.goodsType = '';
			e.map((val, index) => {
				this.model.goodsType += this.model.goodsType == '' ? val.label : '-' + val.label;
			})
		},
		
		// 获取验证码
		getCode() {
			if(this.$refs.uCode.canGetCode) {
				// 模拟向后端请求验证码
				uni.showLoading({
					title: '正在获取验证码',
					mask: true
				})
				setTimeout(() => {
					uni.hideLoading();
					// 这里此提示会被this.start()方法中的提示覆盖
					this.$u.toast('验证码已发送');
					// 通知验证码组件内部开始倒计时
					this.$refs.uCode.start();
				}, 2000);
			} else {
				this.$u.toast('倒计时结束后再发送');
			}
		},
		errorChange(index) {
			if(index == 0) this.errorType = ['message'];
			if(index == 1) this.errorType = ['toast'];
			if(index == 2) this.errorType = ['border-bottom'];
			if(index == 3) this.errorType = ['border'];
		}
	}
};
</script>

<style scoped lang="scss">
.wrap {
	padding: 30rpx;
}

.agreement {
	display: flex;
	align-items: center;
	margin: 40rpx 0;

	.agreement-text {
		padding-left: 8rpx;
		color: $u-tips-color;
	}
}
</style>
