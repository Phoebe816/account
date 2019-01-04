<!-- 1、模板 -->
<template>
  <el-main>    
    <el-container>
      <el-aside width="200px" style="padding: 60px 0 0;">
        <homemenu></homemenu>
      </el-aside>
      <el-main class="re-content">
        <wagecalculator style="width:800px;"></wagecalculator>
      </el-main>
    </el-container> 
  </el-main>
</template>
<!-- 2、行为：处理逻辑 -->

<script>
import Homemenu from './Homemenu'
import wagecalculator from './WageCalculator'

export default {
  name: 'home',
  data(){
    return {
        
      }
    },
  components:{
    "homemenu":Homemenu,
    wagecalculator
   
  }
 
}
</script>
<!-- 3、样式：解决样式 -->
<style scoped lang="stylus" rel="stylesheet">
@import '../../assets/css/index.styl'
.el-main{
  padding 20px 50px;
}
.re-content{
  margin: 0 50px;
  padding  0
  max-width  ($width--max+50)px
}
</style>

