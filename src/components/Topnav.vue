<!-- 1、模板 -->
<template>
  <el-header class="topnav"> 
    <div class="nav" >
      <div class="logo">
        <h1>
          <router-link to="/" title="天使记账首页">天使记账</router-link>
        </h1>     
      </div>      
      <div class="navbox" ref="topnavBox"> 
        <span id="diamond" class="diamond" :style="navStyle" :class="{hidden: active===current}"></span>
        <ul style="overflow:hidden;height:100%;">          
          <li class="navlist" style="width: 80px;"
            :class="{bigsize:index===active, changesize:index===current}" 
            v-for="(navtitle, index) of navTitle" 
            :key="navtitle.id"
            :router=navtitle.pathTo
            @click="active=index;"
            @mouseover="diamondMove(index)" @mouseout="diamondMove(active)">            
            <!-- <span slot="title"> {{ navtitle.name }} </span> -->
            <router-link :to=navtitle.pathTo> {{ navtitle.name }} </router-link>        
          </li>           
        </ul>
      </div> 
      <ul class="login-menu" v-if="userInfo">
        <li>
          <el-dropdown @command="handleCommand">
            <span class="el-dropdown-link cursor-pointer">              
                你好！{{userInfo.account ? userInfo.account : userInfo.phoneNum}}               
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>
                <router-link :to="'/info'">
                  <i class="icon iconfont icon-information-blue"></i>
                  <span>个人中心</span>
                </router-link>
              </el-dropdown-item>
              <el-dropdown-item class="myBookshelf">
                <router-link :to="'/bookshelf'" tag="span">
                  <i class="icon iconfont icon-bookshelf-blue"></i>
                  <span>我的书架</span>
                </router-link>
              </el-dropdown-item> 
              <el-dropdown-item command="exit" class="cursor-pointer">
                <i class="icon iconfont icon-blueexit"></i>
                <span>退出</span>                
              </el-dropdown-item>      
            </el-dropdown-menu>
          </el-dropdown>
        </li>
        <li><!-- v-if="userInfo && userInfo.roleName === 'user'" -->
          <el-badge :value="shopCartNum" class="myBadgecart" :hidden="shopCartNum === 0">
            <router-link :to="'/shopCart'" tag="span">
              <i class="icon iconfont icon-smilecart"></i>
              <span class="messageText">购物车</span>
            </router-link>
          </el-badge>
        </li>
        <li>
          <el-popover
            popper-class="messagePopper"
            placement="bottom"
            width="300"
            v-model="visiblity"
            trigger="click">
            <div class="messageBox">
              <div class="title">消息</div>
              <div class="message" v-if="messageData.length === 0">暂无通知</div>
              <div v-else>
                <div class="message" v-for="item in messageData" @click="readMessage(item.id)" :key="item.rid">
                  <router-link :to="{
                    name:item.route,
                    query:{
                      messageId:item.rid
                    }
                  }">{{'【' + item.message + '】'}}</router-link>
                  <span>{{item.message_time}}</span>
                </div>
              </div>
            </div>
            <el-badge slot="reference" :value="messageData.length" class="item myBadge" :hidden="messageData.length === 0">
              <span class="messageText">消息({{ messageData.length }})</span>
            </el-badge>
          </el-popover>
        </li>
      </ul>
      <!-- 未登录展示 -->
      <ul class="usermsg" v-else>   
           <li class="userlist grey">
              <router-link to="/login" id="loginIn" class="system">登录</router-link>
              <span>｜</span>
              <router-link to="/register" class="system"> 注册 </router-link>
           </li>         
      </ul> 
    </div>         
  </el-header>
</template>
<!-- 2、行为：处理逻辑 -->
<script>
import {loginOutApi,getAllMessageApi,readMessageApi} from '@/apis'
import {mapGetters,mapActions} from 'vuex'
export default {
  name: 'topnav',
  props:["Lists"],
  data: function(){
    return {
      navTitle:[
        { name: "首页", pathTo: "/", id:"homePage"},
        { name: "孕育", pathTo: "/pregnant", id:"pregnantPage"},
        { name: "购物", pathTo: "/shopping", id:"shoppingPage"},
        { name: "旅游", pathTo: "/tour", id:"tourPage"},             
        { name: "互助", pathTo: "/mutualaid", id:"mutualAidPage"},
        { name: "阅读", pathTo: "/read", id:"read"},
        { name: "娱乐", pathTo: "/amusement", id:"amusement"},
        // { name: "宣管", pathTo: "/dypropaganda", id:"propaganda"}     
      ],
      current: 0,
      active: 0,
      shopCartCount:0,
      messageData:[],
      visiblity:false,
      navStyle:{
        left: '10px',
        width: '60px'
      },
      state3: ''   
    }
  },
  watch:{
    $route(to,from){
      this.active = this.navTitle.findIndex(item => {
        return item.pathTo === to.path;
      });
      this.diamondMove(this.active);
    }
  },
  computed: {
    ...mapGetters(['userInfo','shopCartNum'])
  },
  methods: {
    ...mapActions(['UserLoginOut','ChangeShopCart']),
    diamondMove(index){     
      this.current = index;         
      this.navStyle.left =  10+80* index + 'px';     
    },
    handleCommand(command){     
      if(command === 'exit'){
        loginOutApi().then((res) => {
          if(res.status === 200){
            let _data = res.data;
            if(_data.success === true){
              this.$router.push('/login');
              this.UserLoginOut();
            }
            this.$message({
              message:_data.operateMessage,
              type:_data.success === true ? 'success' : 'error'
            })
          }
        }) 
      }   
    },
   fetchData(){
      getAllMessageApi().then(res => {
        if(res.status === 200){
          this.ChangeShopCart(res.data.cart)         
        }
      })
    },
    readMessage(id){
      this.visiblity = false
      readMessageApi(id).then(res => {
        if(res.status === 200){
          this.fetchData()
        }
      })
    },
   
    handleSelect(item) {
      console.log(item);
    },
    handleIconClick(ev) {
      console.log(ev);
    }          
  },
  mounted(){   
    this.fetchData();
  }
}
</script>
<!-- 3、样式：解决样式 -->
<style scoped lang="stylus" rel="stylesheet">
@import '../assets/css/index.styl' 
.topnav{
  width:100%;   
  background: linear-gradient(to right, #4b8adc, #8acfee);
  position: fixed;
  top:0px;  
  z-index: 99; 
  padding: 0 50px;  
 } 
 .topnav .nav{
   background: url('../assets/propaganda/headerthemeimgstwo/top_bg.png') no-repeat;
  background-size: 100% 100%;
  width: 100%;
  max-width :1250px;
  height: 100%;
  margin: 0px auto;
  font-size: 16px;
  line-height: $heght--nav;    
   .logo{
    width: 150px;
    height: 100%; 
    float: left;
    padding-left: 30px;
    background: url("../../static/money.png") no-repeat -20px 0;
    background-size: contain;
    box-sizing :border-box;
    h1{
      margin:0;      
      a{
        color :#f3d608;
        font-weight : bolder;
        background-image: -webkit-gradient(linear, 0 0, 0 bottom, from(#3c60e6), to(rgba(51, 51, 51, 1)));
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
      }
    }
  }  
   & ul>li>a{
      font-size 16px
      color:#f7ef05;
      font-weight:bold;
      background-image: -webkit-gradient(linear, 0 0, 0 bottom, from(#f7ef05), to(#fff));
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent; 
   }   
     .changesize{
        transition: font 0.5s;
        -webkit-transition: font 0.5s; /* Safari */ 
        &:hover a{
          font-size: 20px;
          font-weight: bold;
        } 
      }
      .bigsize a{
        font-size: 20px;
        font-weight: bold;    
      }
 }
 .navlist{         
      display: inline-block;
      position: relative;
      text-align: center; 
 }

 .navbox{  
  height: 100%;     
  float: left; 
  position: relative; 
  display: inline-block;
  margin-right:20px;
  width: calc(100% - 660px);
 } 
  
 .diamond{
  position: absolute;
  height: 100%;
  background: url("../assets/img/diamond.png") no-repeat;  
  background-size :200% 200%;
  opacity:0.3;  
  -moz-opacity:0.3;
  -khtml-opacity: 0.3;
 } 
 .hidden{
   display :none;
  }
 .usermsg{
  float: right;    
 }
 .userli{
  width: 150px;
  height: 100%;  
  display: inline-block;  
  float: right;  
  text-align: center;
  color: #ccc;
 } 
 .system:hover{
  color:#FF83FA;
 }

 .bigsize::before{  
   content:"";
   background: url("../assets/img/diamond.png") no-repeat; 
   background-size :200% 200%;
  opacity:0.3;  
  -moz-opacity:0.3;
  -khtml-opacity: 0.3;
  z-index: -1;
  width:$heght--nav;
  height:100%;
  position:absolute;
  top:0;
  left:10px;
 }
 .messageText{
  line-height 48px
  font-size 12px
  cursor pointer
}
.login-menu {
  float right
  height 100%
  & > li {
    float left
    height inherit
    padding-left 12px
    font-size 12px
    vertical-align middle
  }
  & i{   
    vertical-align middle
    cursor pointer
  }
  & a{
      color $--color-ablue
  }
}
.el-badge.myBadgecart /deep/ .el-badge__content.is-fixed {
  margin-top: 15px;
  margin-right:35px;
  border: none;
}
.el-dropdown{
  color $--color-ablue
  padding-top 2px
}
</style>