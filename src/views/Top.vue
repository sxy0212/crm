<template>
	<section>
		<div class="header">
			<div class="title">	言通智能CRM</div>
			<div class="drop">
			<span @click="shuxian" style="margin-right:20px;cursor:pointer;">刷新</span>
			<el-dropdown trigger="hover" >
				<span class="el-dropdown-link userinfo-inner"><img :src="this.sysUserAvatar" />用户名</span>
				<el-dropdown-menu slot="dropdown" style="cursor: pointer;">
					<el-dropdown-item>我的消息</el-dropdown-item>
					<el-dropdown-item>设置</el-dropdown-item>
					<el-dropdown-item divided @click.native="logout">退出登录</el-dropdown-item>
				</el-dropdown-menu>
			</el-dropdown>
		</div>
		</div>
	</section>
</template>

<script type="text/ecmascript-6">
import store from '../vuex/store.js'
  	export default {
			inject:['reload'],
			data() {
				return {
					collapsed:false,
					sysUserName: '',
					sysUserAvatar: '',
					form: {
						name: '',
						region: '',
						date1: '',
						date2: '',
						delivery: false,
						type: [],
						resource: '',
						desc: ''
					},
			}
		},
		methods: {
			onSubmit() {
				console.log('submit!');
			},
			handleopen() {
				//console.log('handleopen');
			},
			handleclose() {
				//console.log('handleclose');
			},
			handleselect: function (a, b) {
			},
			shuxian(){
				this.reload()
			},
			//退出登录
			logout() {
				this.$confirm('确认退出吗?', '提示', {
					type: 'warning'
				}).then(() => {
				
				}).catch(() => {

				});


			},
		
		},
		mounted() {
			var user = sessionStorage.getItem('user');
			if (user) {
				user = JSON.parse(user);
				this.sysUserName = user.name || '';
				this.sysUserAvatar = user.avatar || '';
			}

		}
	}

</script>

<style lang="scss" scoped >	
	#app .title{
		color:#fff;
	}
	.title{
		font-size:18px;
		height:60px;
		line-height:60px;
		float:left;
		padding-left:20px;
	}
	.header{
		    overflow: hidden;
    width: 100%;
    background: #23262E;
		height:60px;
	}
	.drop{
		float:right;
		cursor:pointer;
		color:#fff;
		.el-dropdown{
			padding-right:10px;
			color:#fff;
			height:60px;
			line-height:60px;
			font-size:18px;
		}
	}
	
</style>