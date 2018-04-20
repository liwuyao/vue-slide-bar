<template>
	<div class="slide-bar2">
		<div class="slide-bar2-box" v-bind:class="{ openActive: isOpenActive }">
			<div style="float: left;" class="slide-bar2-left">
				<div v-on:click="sideBarOpen()" style="text-align: center;padding: 20px 0;">
					<i class="iconfont icon-el-icon-karakal-oppen"></i>
				</div>
				<div>
					<div v-for="item in data" class='slide-nav-hover'>
						<first-nav :message="item" :open='open' :rightStatu='slideRight' :currentNav='currentNav' v-on:send="sendMessae" :promptElemStatu='promptElemStatu'></first-nav>
					</div>
				</div>
			</div>
			<div  v-if="slideRight" class="slide-bar2-right">
				<p style="padding: 25px;font-weight: bolder;border-bottom: 1px solid gainsboro;" v-if='slideRightChild'>{{rightData.name}}</p>
				<div v-for="item in rightData.child" v-if='slideRightChild'>
					<div v-if="item.list">
						<select-list :message='item'></select-list>
					</div>
					<div v-else  class='slide-nav-hover'>
						<router-link :to='item.index' style="padding: 20px 0 20px 30px;display: block;">
						  	<span>{{item.title}}</span>
						</router-link>
					</div>
				</div>
				<img src="../../assets/iocn/close.png" v-on:click="closeRight()" style="position: absolute;right: -41px;top: 40%;transform: translate(0,-50%);z-index: 10;cursor: pointer;"/>
			</div>
		</div>
	</div>
</template>

<script>
//      一级导航
		var firstNav = {
		  template:`
		  <div>
		  	<div v-if="message.index" v-on:click="onlyLink()" style="padding: 10px 0;">
				<el-tooltip class="item" effect="dark" :content="message.name" placement="right" :disabled="promptElemStatu">
			      	<router-link :to="message.index">
			  			<div style='width:60px;text-align: center;display:inline-block'>
				  			<i :class="message.icon"></i>
				  		</div>
				  		<div style="display:inline-block" v-if="open">{{message.name}}</div>
					</router-link>
			    </el-tooltip>
		  	</div>
		  	<div v-else v-on:click="sendMessage()" style="padding: 10px 0;">
		  	<el-tooltip class="item" effect="dark" :content="message.name" placement="right" :disabled="promptElemStatu">
		  		<div>
		  			<div style='width:60px;text-align: center;display:inline-block'>
			  			<i :class="message.icon"></i>
			  		</div>
			  		<div style="display:inline-block" v-if="open">{{message.name}}</div>
		  		</div>
		  	 </el-tooltip>
		  	</div>
		  </div>
		  `,
		  props: ['message','open','rightStatu','currentNav','promptElemStatu'],
		  data(){
		  	return{
		  		test:{},
		  		disabled:false,
		  	}
		  },
		  created(){
		  	this.disabled = this.promptElemStatu;
		  },
		  methods:{
				sendMessage(){
						if(this.rightStatu === false){
							this.$emit('send', {rightStatu:true,message:this.message});
						}
						if(this.rightStatu === true){
							if(this.message === this.currentNav){
								this.$emit('send', {rightStatu:false,message:''});
							}else{
								this.$emit('send', {rightStatu:true,message:this.message});
							}
						}
				   	},
				onlyLink(){
					console.log(this.message.blank);
					if(!this.message.blank){
						if(this.rightStatu === true){
							this.$emit('send', {rightStatu:false,message:''});
						}
					}
				}
		   	}
		}
//		下拉导航
		var selectList={
			props: ['message'],
			template:`
				<div class="select-list">
					<div style="overflow:hiden;">
						<div @click="rightSelect(message.className)" style="padding: 20px 0 20px 30px;cursor: pointer;">
							<span>{{message.title}}</span>
							<div style="display:inline-block" class="select-animation">
								<i class="iconfont icon-el-icon-karakal-xiala"></i>
							</div>
						</div>
						<div class="right-select" :class="message.className">
							<div v-if='rightNav'>
								<div v-for='item in message.list' class='slide-nav-hover'>
									<router-link :to='item.index'  style="padding: 20px 0 20px 30px;display:block;">
									  	<span>{{item.name}}</span>
									</router-link>
								</div>
							</div>
						</div>
					</div>
				</div>
			`,
			data(){
				return{
					selectHeight:0,
					rightWidth:0,
					rightNav:false,
					resetStatu:true,
					oldMessage:{}
				}
			},
			created(){
				this.oldMessage = this.message;
			},
			updated(){
//				数据更新后重置下拉列表
				if(this.rightNav && this.oldMessage != this.message){
					this.oldMessage = this.message;
					var calssName = '.'+this.message.className;
					var elm=document.querySelector(calssName);
					var child =elm.previousElementSibling.children[1];	
				var timer = setInterval(()=>{
							icon2(child);
							this.selectHeight -=10;
							elm.style.height= this.selectHeight  +'px';
							if(this.selectHeight <= 0){
								clearInterval(timer);
								this.rightNav =false;
							}
						},10)
				}
				function icon2(elm){
						var deg = 180;
						var timer2=setInterval(()=>{
							deg -= 20;
							var degString = 'rotateZ('+deg+'deg)';
							elm.style.transform= degString;
							if(deg <=0){
								clearInterval(timer2);
							}
						},20)
					}
			},
			 methods:{
//		右侧下拉动效
				rightSelect(title){
					var className = '.'+title
					var elm=document.querySelector(className);
					var child =elm.previousElementSibling.children[1];
					var num = this.message.list.length;
					var height = 61;
					var maxHeight = num*height;
					this.oldMessage = this.message;
					if(!this.rightNav){
							icon1(child);
							var timer = setInterval(()=>{
							this.selectHeight +=10;
							elm.style.height= this.selectHeight  +'px';
							if(this.selectHeight >= maxHeight){
								clearInterval(timer);
								this.rightNav=true;
							}
						},10)
					}else{
						var timer = setInterval(()=>{
							icon2(child);
							this.selectHeight -=10;
							elm.style.height= this.selectHeight  +'px';
							if(this.selectHeight <= 0){
								clearInterval(timer);
								this.rightNav =false;
							}
						},10)
					}
					function icon1(elm){
						var deg = 0;
						var timer2=setInterval(()=>{
							deg += 20;
							var degString = 'rotateZ('+deg+'deg)';
							elm.style.transform= degString;
							if(deg >=180){
								clearInterval(timer2);
							}
						},20)
					}
					function icon2(elm){
						var deg = 180;
						var timer2=setInterval(()=>{
							deg -= 20;
							var degString = 'rotateZ('+deg+'deg)';
							elm.style.transform= degString;
							if(deg <=0){
								clearInterval(timer2);
							}
						},20)
					}
				}
			 }
		}
//	主体代码
	export default {
		naem:'test',
		props: ['message'],
		components:{
			'first-nav': firstNav ,
			'select-list':selectList
		},
		data(){
			return{
				isOpenActive:false,
				open: false,
				slideRight:false,
				slideRightChild:false,
				promptElemStatu:false,
				oldWidth:60,
				selectHeight:0,
				rightWidth:0,
				rightNav:'',
				test:'a',
				currentNav:'',
				data:[],
				rightData:{}
			}
		},
		created(){
			this.data = this.message
		},
		methods:{
			sideBarOpen(){
				let elm = document.querySelector('.slide-bar2-left');
				if(!this.open){
					var timer = setInterval(()=>{
						this.oldWidth += 10
						elm.style.width= this.oldWidth +'px'
						if(this.oldWidth >= 160){
							clearInterval(timer);
							this.open= !this.open;
							this.promptElemStatu = true;
						}
					},10)
				}else{
					this.open= !this.open;
					var timer = setInterval(()=>{
						this.oldWidth -= 10
						elm.style.width= this.oldWidth +'px'
						if(this.oldWidth <= 60){
							clearInterval(timer)
							if(!this.slideRight ){
								this.promptElemStatu = false;
							}
						}
					},10)
				}
			},
//			发送信息
			sendMessae(data){
				if(data.rightStatu){
					this.slideRight = true;
					this.slideRightChild = true;
					this.promptElemStatu = true;
					this.rightData = data.message;
					this.currentNav = data.message;
					if(this.rightWidth >= 180) return;
					setTimeout(()=>{
						let elm = document.querySelector('.slide-bar2-right');
						var timer = setInterval(()=>{
							this.rightWidth += 20;
							elm.style.width= this.rightWidth +'px';
							if(this.rightWidth >= 180){
								clearInterval(timer)
							}
						},10)
					})
				}else{
					this.slideRightChild = false;
					if(!this.open){
						this.promptElemStatu = false;
					}
					setTimeout(()=>{
						let elm = document.querySelector('.slide-bar2-right');
						var timer = setInterval(()=>{
							this.rightWidth -= 20;
							elm.style.width= this.rightWidth +'px';
							if(this.rightWidth <= 0){
								clearInterval(timer);
								this.slideRight = false;
							}
						},10)
					})
				}
			},
			closeRight(){
				console.log(this.slideRight)
				if(this.slideRight){
					this.slideRightChild = false;
					if(!this.open){
						this.promptElemStatu = false;
					}
					setTimeout(()=>{
						let elm = document.querySelector('.slide-bar2-right');
						var timer = setInterval(()=>{
							this.rightWidth -= 20;
							elm.style.width= this.rightWidth +'px';
							if(this.rightWidth <= 0){
								clearInterval(timer);
								this.slideRight = false;
							}
						},10)
					})
				}
			}
		}
		
	}
</script>

<style >
	.slide-bar2{
		height: 100%;
	}
	.slide-bar2-box{
		display: flex;
		flex-direction: row;
		height: 100%;
	}
	.slide-bar2-left{
		width: 60px;
		height: 100%;
		color: white;
		background: #1888f7;
	}
	.slide-bar2-left a{
		color: white !important;
		text-decoration: none;
	}
	.slide-bar2 .openActive{
		width: 120px;
	}
	.slide-bar2-right{
		position: relative;
		display: inline-block;width: 0px;height:100%;
		border-right: 1px solid gainsboro;
	}
	.slide-bar2-right a{
		color: black;
	}
	.slide-bar2 .router-link-active{
		text-decoration: none;
		color: orange !important;
	}
	.slide-bar2 .router-link-active i{
		color: orange !important;
	}
	.slide-bar2 .right-select{
		background: #f6f6f6;
		overflow: hidden;
	}
	.slide-bar2-icon{
		font-size: 18px;
		color: white;
	}
	.select-animation{
		margin-left: 30px;
	}
</style>