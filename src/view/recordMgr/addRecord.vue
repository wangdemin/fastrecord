<template>
  <Card>
    <p slot="title">添加备案</p>
    <div>
      <ReportCode v-show="step === 1" @toStep2="showTemple"></ReportCode>
      <SelectSample ref="selectSample" v-show="step === 2" @toStep3="showRecordTab"></SelectSample>
      <Tabs type="card" v-show="step === 3">
        <TabPane label="能效指标">
          <!-- <EnergyIndicators ref="energyIndicators"></EnergyIndicators> -->
          <Html23 v-if="category == 23" ref="energyIndicators23" @prevStep="step = 2"></Html23>
          <Html31 v-if="category == 31" ref="energyIndicators31" @prevStep="step = 2"></Html31>
          <Html33 v-if="category == 33" ref="energyIndicators33" @prevStep="step = 2"></Html33>
          <Html47 v-if="category == 47" ref="energyIndicators47" @prevStep="step = 2"></Html47>
        </TabPane>
        <!-- <TabPane label="性能指标">
          <PerformanceIndicators></PerformanceIndicators>
        </TabPane>
        <TabPane label="安全指标">
          <SecurityIndicators></SecurityIndicators>
        </TabPane> -->
      </Tabs>
    </div>
    <Modal v-model="modal1" title="提示：" @on-ok="setNoTips">
      <p style="font-size:18px;margin-bottom:10px;">已将实验师报告相关数据带入了对应表单项。如果您发现数据有问题，请点击右侧<Icon type="ios-help-circle-outline" size="20"></Icon>图标，一键上报问题。我们会及时修改！</p>
      <CheckboxGroup v-model="noTips">
        <Checkbox label="1">不再出现该提示</Checkbox>
      </CheckboxGroup>
    </Modal>
  </Card>
</template>
<script>
import {setCookie,getCookie} from '@/libs/util.js'
import ReportCode from './reportCode'
import SelectSample from './selectSample'
import PerformanceIndicators from './performanceIndicators'
import SecurityIndicators from './securityIndicators'
import Html47 from './energyIndicators/html47'
import Html33 from './energyIndicators/html33'
import Html31 from './energyIndicators/html31'
import Html23 from './energyIndicators/html23'

export default {
  data () {
    return {
      step: 1,
      category: this.$store.state.app.modelNo,
      noTips: [],
      modal1: false
    }
  },
  components: {
    ReportCode,
    SelectSample,
    PerformanceIndicators,
    SecurityIndicators,
    Html47,
    Html33,
    Html31,
    Html23
  },
  mounted () {
    if(this.$route.params.type){
      if(this.$route.params.type==='update' || this.$route.params.type==='extend'){
          this.$store.commit('setPageType', this.$route.params.type)
          this.$store.commit('setUpdateId',this.$route.params.id)
      }
    }else{
          this.$store.commit('setPageType', '')
          this.$store.commit('setUpdateId','')
    };
    if (this.$route.params.step && this.$route.params.step === 3) {
      this.step = 3
      if (!getCookie('noTips') || getCookie('noTips') !== '1') {
        //this.modal1 = true
      }
      if(this.$route.params.draftData){
        this.$refs['energyIndicators' + this.category].fillDraftData(this.$route.params.draftData)
      }else if(this.$route.params.extendData){
        this.$refs['energyIndicators' + this.category].fillExtendData(this.$route.params.extendData)
      }
    }
  },
  methods: {
    setNoTips () {
      setCookie('noTips',this.noTips.join(''),1)
      console.log(this.noTips)
    },
    showTemple () {
      this.$refs.selectSample.getSampleList()
      this.step = 2
      this.category = this.$store.state.app.modelNo
    },
    showRecordTab () {
      this.step = 3
      if (!getCookie('noTips') || getCookie('noTips') !== '1') {
        //this.modal1 = true
      }
      this.$refs['energyIndicators' + this.category].fillDefaultData()
    }
  }
}
</script>
<style lang="less" scoped>
.ivu-radio-large .ivu-radio-inner{
  top:20px!important;
  left:62px!important;
}
.ivu-icon-ios-help-circle-outline:before{
  content:"\F446";
}
</style>
