<!-- 1、模板 -->
<template>     
<div>
  <h2 style="text-align:center;color: #4b8adc;font-size: 30px;">个人工资计算器</h2>
  <div class="cal-container">
    <el-row type="flex" class="row-bg" style="border-bottom: 1px solid #ddd;">
      <el-col :span="11"  class="col-left">
        <el-form ref="calbeforform" label-position="left" :model="calbefore" label-width="100px">
          <el-form-item label="所在地：">
            <el-select v-model="calbefore.region" placeholder="北京">
              <el-option label="上海" value="shanghai"></el-option>
              <el-option label="北京" value="beijing"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="收入类型：">
            <el-select v-model="calbefore.wagetype" placeholder="工资、薪金所得（月薪）">
              <el-option label="工资、薪金所得（月薪）" value="wage"></el-option>
              <el-option label="年终奖所得" value="cash"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="专项扣除：">
            <el-input placeholder="" v-model="calbefore.deduction">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item label="税前月收入：">
            <el-input placeholder="" v-model="calbefore.wagebefore">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary">计算</el-button>
            <el-button type="warning">重置</el-button>
          </el-form-item>
        </el-form>
      </el-col>
      <el-col :span="11" style="margin-left:20px;">    
        <el-row>
            <p style="font-weight:bold;font-size:16px;text-align:center;">计算结果</p>
        </el-row>    
        <el-form ref="calbeforform" label-position="left" :model="calresult" label-width="100px">   
          <el-form-item label="五险一金：">
            <el-input placeholder="" v-model="calresult.totalPayment">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>      
          <el-form-item label="税后月收入：">
            <el-input placeholder="" v-model="calresult.income" :disabled="true">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item label="缴纳个税：">
            <el-input placeholder="" v-model="calresult.tax" :disabled="true">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary">个人所得税税率表</el-button>  
          </el-form-item>          
        </el-form>
      </el-col>
    </el-row>
    <p><span style="font-weight:bold;">社保与公积金：</span>(可手动调整参数)</p>
    <el-row type="flex" class="row-bg">
      <el-col :span="11" class="col-left">
        <p>缴费基数：</p>
        <el-form ref="insurancebaseform" label-position="left" :model="insurancebase" label-width="80px">
          <el-form-item label="社保">
            <el-input placeholder="" v-model="insurancebase.security">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item label="公积金">
            <el-input placeholder="" v-model="insurancebase.funds">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
          <el-form-item label="封顶数">
            <el-input placeholder="" v-model="insurancebase.toplimit">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
           <el-form-item label="免征额">
            <el-input placeholder="" v-model="insurancebase.allowance">
              <template slot="append">元</template>
            </el-input>
          </el-form-item>
        </el-form>
      </el-col>
      <el-col :span="11" style="padding-left: 20px;">
        <p>缴费结果：</p>
        <el-form ref="form" label-position="left" :model="securityratio" label-width="80px">
          <el-form-item label="养老">
            <el-col :span="4">
              <el-input placeholder="" v-model="securityratio.retire"></el-input>
            </el-col>
            <el-col :span="2">%</el-col>
            <el-col :span="14" style="margin-left:20px;">
              <el-input placeholder="" v-model="securityform.retire">
                <template slot="append">元</template>
              </el-input>
            </el-col>            
          </el-form-item>
          <el-form-item label="医疗">
            <el-col :span="4">
              <el-input placeholder="" v-model="securityratio.medical"></el-input>
            </el-col>
            <el-col :span="2">%</el-col>
            <el-col :span="14" style="margin-left:20px;">
              <el-input placeholder="" v-model="securityform.medical">
                <template slot="append">元</template>
              </el-input>
            </el-col>            
          </el-form-item>
          <el-form-item label="失业">
            <el-col :span="4">
              <el-input placeholder="" v-model="securityratio.jobness"></el-input>
            </el-col>
            <el-col :span="2">%</el-col>
            <el-col :span="14" style="margin-left:20px;">
              <el-input placeholder="" v-model="securityform.jobness">
                <template slot="append">元</template>
              </el-input>
            </el-col>   
          </el-form-item>
           <el-form-item label="公积金">
            <el-col :span="4">
              <el-input placeholder="" v-model="securityratio.funds"></el-input>
            </el-col>
            <el-col :span="2">%</el-col>
            <el-col :span="14" style="margin-left:20px;">
              <el-input placeholder="" v-model="securityform.funds">
                <template slot="append">元</template>
              </el-input>
            </el-col>   
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>  
</div>
</template>
<!-- 2、行为：处理逻辑 -->
<script>

export default {
  name: 'wagecalculator',
  data(){
    return{     
      calbefore:{
        region:'北京',
        wagetype: '',
        wagebefore: '',
        deduction: ''
      },
      insurancebase:{
        security: '',
        funds: '',
        toplimit: '',
        allowance: ''
      },
      securityratio:{
        retire: '',
        medical: '',
        jobness: '',
        funds: ''
      },
      securityform:{
        retire: '',
        medical: '',
        jobness: '',
        funds: ''
      },
      calresult:{
        totalPayment: '',
        income: '',
        tax: ''
      },
      totalPayment: '',
      currentDate: new Date()
    }
  }
 
}
</script>
<!-- 3、样式：解决样式 -->
<style scoped lang="stylus" rel="stylesheet">
@import '../../assets/css/index.styl' 
.cal-container {
  background:  #FFFFdd;
  border: 20px solid #B4EEB4;
  height: 600px;
  padding:20px;
}
.row-bg{  
  padding: 10px 0;
  p{
    margin-top:0;
  }
}
.col-left{
  border-right: 1px solid #ddd;
  padding-right:20px;
}
</style>