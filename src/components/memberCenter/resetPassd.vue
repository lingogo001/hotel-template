<template>
	<div id="resetPassd" style="position: relative;">
		<section class="main fixed">
			<article class="content">
				<div class="login-head">
					<img src="../../images/login_in_head_img@2x.png"/>
				</div>				
				<div class="pass-input pass-name">
					<span>用户名</span><input type="text" v-model="resetParam.username" placeholder="请输入您的用户名"/>
				</div>
				<div class="pass-input pass-name">
					<span>验证码</span><input type="tel" v-model="resetParam.sms" placeholder="请输入邮箱收到的验证码" />
				</div>
				<div class="pass-input">
					<span>新密码</span><input type="password" v-model="resetParam.password" placeholder="请设置登录密码" />
				</div>
				<div class="pass-input">
					<span>重输密码</span><input type="password" id="pwdAgin" v-on:blur="chanePwd()" placeholder="请再一次输入密码" />
					<p id="checkPwdr">*两次密码输入的不一致</p>
				</div>
				<div class="pass-go"><span class="pass-goto" v-on:click="setPwd()" >完成</span></div>
			</article>		
		</section>
	</div>	
</template>
<style>
	#resetPassd{
		background-color: #fff;
	}
	#resetPassd #checkPwdr{
		display: none;
		color: red;
	}
	.pass-input{
		width: 84%;
		border-bottom: 1px solid #ccc;
		margin: 0 8%;
		padding-top: 2rem;
		padding-bottom: 0.16666666666666666rem;
	}
	.pass-input > span{
	    font-size: 1.1333333333333333rem;
	    color: #333;
	    display: inline-block;
	    width: 25%;
	    text-align: right;		
	}
	.pass-input > input{
		margin-left: 6%;
		width: 68%;
		height: 2.5rem;
		font-size: 1rem;
		color: #999;
		border: 0;
	}
	.login-head{
		position: relative;
		width: 100%;
		height: 8.233333333333333rem;
		background: linear-gradient(to right top,#14b6f5,#6ed6ff);
	}
	.login-head img{
		width: 5.466666666666667rem;
		height: 5.466666666666667rem;
		position: absolute;
		display: inline-block;
		bottom: -1rem;
		left: 50%;
		transform: translateX(-50%);
	}
	.pass-go{
	    width: 84%;
	    line-height: 2.7rem;
	    margin: 8%;
	    font-size: 1rem;		
	}
	.pass-go span{
	    display: block;
	    text-align: center;	
	    border-radius: 40px;	
	}
	.pass-go span.pass-goto{
		background-color: #14B6F5;
		color: #fff;
	}
</style>
<script type="text/javascript">
    export default {
        data: function(){
        	return {
        		resetParam:{
        			shopid:configuration.global.shopid,
        			username:"",
        			sms:"",
        			password:""
        		}
        	}
        },    	
        mounted: function () {
        	var scrollerConHeight = $(window).height() - $("#afui #footer").height();//页面内容高度
            $("#resetPassd").css("overflow-y", "auto").css("overflow-x","hidden").css("height", scrollerConHeight + "px");        	
        	this.resetParam.username = this.$route.query.username||"";
        },
        methods: {
        	isEnNumeric:function(strValue){
        		// 判断是否为英文、数字、字母或_
				var pattern = /^[_0-9a-zA-Z]+$/;  
				return pattern.test(strValue);        		
        	},        	
            setPwd:function(){
        		if(this.checkParam()){
			        this.$http.post(configuration.global.serverPath + "/api/login/retrievePassword",this.resetParam,{headers: {'Content-Type': 'application/x-www-form-urlencoded'},emulateJSON:true}).then(function (response) {
			         	var results = response.data;
		                if(results.code === 200){
		                	layerUtils.iAlert("重置密码成功,请重新登录",function(){
		                		this.$router.push({name:"login"});
		                	}.bind(this));
		                }else if(results.code === -1){
		                    layerUtils.iAlert(results.message);
		                }else{
		                	layerUtils.iAlert(results.message);
		                }
			        },function (response) {
			        	alert(JSON.stringify(response.data));
			        });         			
        		}
        	},
        	chanePwd:function(){
        		document.getElementById("checkPwdr").style.display = this.resetParam.password === document.getElementById("pwdAgin").value?"none":"block";
        		return this.resetParam.password === document.getElementById("pwdAgin").value;
        	},
		    checkParam:function(){
				if(!this.resetParam.username || !this.isEnNumeric(this.resetParam.username)){
					layerUtils.iAlert("请输入正确的用户名");
					return false;
				}
				if(!this.resetParam.sms || this.resetParam.sms.replace(/\s/g,"").length === 0){
					layerUtils.iAlert("请输入验证码");
					return false;
				}
				if(!this.resetParam.password || this.resetParam.password.replace(/\s/g,"").length === 0){
					layerUtils.iAlert("请输入新密码");
					return false;
				}
				if(!this.chanePwd()){
					return false;
				}
			    return true;
		    }
        }
    }
</script>
