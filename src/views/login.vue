<template>
  	<div class="editor-wrapper">  
  		<div class="edit-heading">
  			<router-link class="iconfont icon-fanhui heading-button back-button" to="/">返回</router-link>
  		</div>
    	<div class="login-content centerVertical">
	      	<div class="login-box-wrapper paper">
	      		<div class="login-box centerVertical">
	      			<div class="common-form login-form" v-show="type === '1'">
	      				<input type="text" class="common-input" placeholder="手机号" v-model="login.username">
	      				<input type="password" class="common-input" placeholder="密码" v-model="login.password">
	      			</div>
	      			<div class="common-form login-form" v-show="type === '2'">
	      				<input type="text" class="common-input" placeholder="手机号" v-model="regist.username">
	      				<input type="password" class="common-input" placeholder="密码" v-model="regist.password" @blur="judgePass">
	      				<input type="password" class="common-input" placeholder="确认密码" v-model="regist.confirmPass" @blur="confrimOpt">
	      			</div>
	      		</div>
	      		<div class="login-btn-wrapper flexbox">
	      			<a class="login-btn option-button" @click="submitOpt">{{type === '1' ? '登录' : '注册'}}</a>
	      			<a class="option-btn login-regist-btn centerVertical" @click="changeType">{{type === '1' ? '注册' : '登录'}}</a>
	      			<a class="option-btn forget-btn centerVertical" @click.stop="contact">联系作者</a>
              <div class="fee" v-show="isFee"><img src="../assets/imgs/fee.jpg"></div>
	      		</div>
	      	</div>
    	</div>
  	</div>
</template>

<script>
import MD5 from 'blueimp-md5';
import Func from '../assets/js/common.js'
let phone = Func.getStorage('FeeList_phone') || ''

export default {
  	data(){
    	return {
      		type: '1',
      		login: {
      			'username': phone,
      			'password': ''
      		},
      		regist: {
      			'username': '',
      			'password': '',
      			'confirmPass': ''
      		},
          isFee: false
    	}
  	},
  	mounted() {
      let that = this;
  		document.onclick = function() {
        that.isFee = false;
      };
      document.onkeydown = function(event) {
        var e = event || window.event || arguments.callee.caller.arguments[0];
        if (e && e.keyCode == 13 && that.$route.path === '/login') {
          that.submitOpt();
        }
      };
  	},
  	watch: {
	    '$route': 'initData'
	  },
  	methods: {
  		initData() {
  			this.type = '1';
  			this.login = {
  				'username': '',
      			'password': ''
  			};
  			this.regist = {
  				'username': '',
      			'password': '',
      			'confirmPass': ''
  			};
        this.isFee = false;
  		},
  		changeType() {
  			this.type === '1' ? this.type = '2' : this.type = '1';
  		},
  		contact() {
  			let bol = this.isFee;
        this.isFee = !bol;
  		},
  		judgePass() {
  			let passReg = /^[a-z0-9]+$/g;
  			if (this.regist.password === '') {
  				Func.toast('请输入密码');
				return;
  			}
  			if (!passReg.test(this.regist.password)) {
				Func.toast('密码只能包含英文和数字');
				return;
			}
			if (this.regist.password.length < 6) {
				Func.toast('密码长度至少为6位');
				return;
			}
  		},
  		confrimOpt() {
  			if (this.regist.confirmPass === '') {
  				Func.toast('请输入确认密码');
				return;
  			}
  			if (this.regist.confirmPass.length < 6) {
				Func.toast('密码长度至少为6位');
				return;
			}
  			if (this.regist.password !== this.regist.confirmPass) {
  				Func.toast('请确认密码是否输入正确');
				return;
  			}
  		},
  		submitOpt() {
  			let params = {};
  			let phoneReg = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/;
  			let passReg = /^[a-z0-9]+$/g;
  			if (this.type === '1') {
  				if (!phoneReg.test(this.login.username)) {
  					Func.toast('请输入正确的手机号');
  					return;
  				}
  				if (!passReg.test(this.login.password)) {
  					Func.toast('请输入正确的密码');
  					return;
  				}
  				params.username = this.login.username;
  				params.password = MD5(this.login.password);
  				this.$store.dispatch('login', params);
          Func.setStorage('FeeList_phone', this.login.username);
  			} else {
  				if (!phoneReg.test(this.regist.username)) {
  					Func.toast('请输入正确的手机号');
  					return;
  				}
  				if (this.regist.password === '') {
	  				Func.toast('请输入密码');
					return;
	  			}
  				if (!passReg.test(this.regist.password)) {
  					Func.toast('密码只能包含英文和数字');
  					return;
  				}
  				if (this.regist.password.length < 6) {
  					Func.toast('密码长度至少为6位');
  					return;
  				}
  				if (this.regist.confirmPass === '') {
	  				Func.toast('请输入确认密码');
					return;
	  			}
	  			if (this.regist.confirmPass.length < 6) {
  					Func.toast('密码长度至少为6位');
  					return;
  				}
  				if (this.regist.password !== this.regist.confirmPass) {
	  				Func.toast('请确认密码是否输入正确');
					return;
	  			}
	  			params.username = this.regist.username;
  				params.password = MD5(this.regist.password);
          params.nickname = '游客' + MD5(params.username).substr(0, 6);
	  			this.$store.dispatch('regist', params);
          Func.setStorage('FeeList_phone', this.regist.username);
  			}
  		}
  	}
}
</script>
 
<style lang="less" scoped>
	.login-content{
		height: 100%;
		background-color: #fff;
		.login-box-wrapper{
			width: 400px;
			height: 350px;
			padding: 30px;
			.login-box{
				height: 83%;
				.common-form{
					width: 100%;
					.common-input{
						height: 50px;
						width: 100%;
						font-size: 15px;
						padding: 10px 15px;
						border: 1px solid #999;
						border-radius: 3px;
						outline: none;
						margin-top: 15px;
						&:first-child{
							margin-top: 0;
						}
					}
				}
			}
			.login-btn-wrapper{
        position: relative;
				height: 13%;
				font-size: 13px;
				.login-btn{
					height: 100%;
					width: 40%;
				}
				.option-btn{
					height: 100%;
					-webkit-user-select: none;
					user-select: none;
					&:hover{
						color: #666;
					}
					&.login-regist-btn{
						width: 40%;
					}
				}
        .fee{
          position: absolute;
          right: -55px;
          bottom: 30px;
          width: 200px;
          &:after{
            content: " ";
            position: absolute;
            width: 0;
            height: 0;
            border-left: 10px solid transparent;
            border-right: 10px solid transparent;
            border-top: 10px solid #bbb;
            bottom: 2px;
            background: none;
            left: 50%;
            margin-left: -7px;
          }
          img{
            width: 100%;
          }
        }
			}
		}
	}
</style>