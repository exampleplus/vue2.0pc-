<template>
  <div class="sales-board">
 		<div class="sales-board-intro">
        <h2>流量分析</h2>
        <p>是指在获得网站访问量基本数据的情况下对有关数据进行统计、分析，从中发现用户访问网站的规律，并将这些规律与网络营销策略等相结合，从而发现目前网络营销活动中可能存在的问题，并为进一步修正或重新制定网络营销策略提供依据。当然这样的定义是站在网络营销管理的角度来考虑的</p>
    </div>
     <div class="sales-board-form">
          <div class="sales-board-line">
              <div class="sales-board-line-left">
                  购买数量：
              </div>
              <div class="sales-board-line-right">
              	 <v-counter @on-change="onParamChange('buyNum', $event)"></v-counter>
              </div>
          </div>
          <div class="sales-board-line">
              <div class="sales-board-line-left">
                  产品类型：
              </div>
              <div class="sales-board-line-right">
                  <v-selection :selections="buyTypes" @on-change="onParamChange('buyType', $event)"></v-selection>
                  <v-selection :selections="buyTypes" @on-change="onParamChange('buyType', $event)"></v-selection>
              </div>
              
          </div>
          <div class="sales-board-line">
              <div class="sales-board-line-left">
                  有效时间：
              </div>
              <div class="sales-board-line-right">
                 <v-chooser
                  :selections="periodList"
                  @on-change="onParamChange('period', $event)"></v-chooser>
              </div>
          </div>
          <div class="sales-board-line">
              <div class="sales-board-line-left">
                  产品版本：
              </div>
              <div class="sales-board-line-right">
                  <v-mul-chooser
                  :selections="versionList"
                  @on-change="onParamChange('versions', $event)"></v-mul-chooser>
              </div>
          </div>
          <div class="sales-board-line">
              <div class="sales-board-line-left">
                  总价：
              </div>
              <div class="sales-board-line-right">
                  		{{price}}
              </div>
          </div>
          <div class="sales-board-line">
              <div class="sales-board-line-left">&nbsp;</div>
              <div class="sales-board-line-right">
                  <div class="button" @click="showPayDialog">
                    立即购买
                  </div>
              </div>
          </div>
      </div>
      <my-dialog :is-show="isShowPayDialog" @on-close="hidePayDialog">
      	  <table class="buy-dialog-table">
	          <tr>
	            <th>购买数量</th>
	            <th>产品类型</th>
	            <th>有效时间</th>
	            <th>产品版本</th>
	            <th>总价</th>
	          </tr>
	          <tr>
	            <td>{{ buyNum }}</td>
	            <td>{{ buyType.label }}</td>
	            <td>{{ period.label }}</td>
	            <td>
	              <span v-for="item in versions">{{ item.label }}</span>
	            </td>
	            <td>{{ price }}</td>
	          </tr>
        	</table>
        	 <h3 class="buy-dialog-title">请选择银行</h3>
	        <bank-chooser @on-change="onChangeBanks"></bank-chooser>
	        <div class="button buy-dialog-btn" @click="confirmBuy">
	          		确认购买
	        </div>
      </my-dialog>
      <check-order  :is-show-check-dialog="isShowCheckOrder" :order-id='orderId' @on-close-check-dialog="hideCheckOrder"></check-order>
  </div>
</template>

<script>
	import VSelection from '../../components/selection'
	import VCounter from '../../components/counter'
	import VChooser from '../../components/chooser'
	import VMulChooser from '../../components/multiplyChooser'
	import Dialog from '../../components/dialog'
	import _ from 'lodash'
	import BankChooser from '../../components/BankChooser'
	import CheckOrder from '../../components/CheckOrder'
	
	export default {
	  components: {
	  	VSelection,
	  	VCounter,
	  	VChooser,
	  	VMulChooser,
	  	MyDialog:Dialog,
	  	BankChooser,
	  	CheckOrder
	  },
	  data() {
	  	return {
	  		buyNum: 0,//购买数量
	      buyType: {},//产品类型
	      versions: [],//购买版本
	      period: {},//有效时间
	      price: 0,
	      isShowPayDialog:false,
	      bankId:null,
	      orderId: null,
	      isShowCheckOrder:false,
	  		 versionList: [
		        {
		          label: '客户版',
		          value: 0
		        },
		        {
		          label: '代理商版',
		          value: 1
		        },
		        {
		          label: '专家版',
		          value: 2
		        }
		      ],
		      periodList: [
		        {
		          label: '半年',
		          value: 0
		        },
		        {
		          label: '一年',
		          value: 1
		        },
		        {
		          label: '三年',
		          value: 2
		        }
		      ],
	  		 buyTypes: [
		        {
		          label: '入门版',
		          value: 0
		        },
		        {
		          label: '中级版',
		          value: 1
		        },
		        {
		          label: '高级版',
		          value: 2
		        }
		      ],
	  	}
	  },
	  methods:{
	  	onParamChange (attr,val) {
	  			this[attr] = val;
	  		 this.getPrice();
	  	},
	  	getPrice () {
		  	let buyVersionsArray = _.map(this.versions,(item) => {
		  		return item.value;
		  	})
		  	let reqParams = {
	        buyNumber: this.buyNum,
	        buyType: this.buyType.value,
	        period: this.period.value,
	        version: buyVersionsArray.join(',')
	      }
		  	// console.log(reqParams)
		  	this.$http.get("https://www.easy-mock.com/mock/5b2f513b0e946a3379e72cab/banklist/buy",reqParams).then((res) => {

		  		 let amount = res.body.getPrice.amount;
		  		 this.price = amount;
		  		 
		  	})
	  	},
	  	
	  	showPayDialog() {
	  		this.isShowPayDialog = true
	  	},
	  	hidePayDialog() {
	  			this.isShowPayDialog = false;
	  	},
	  	onChangeBanks (bankObj) {
	  		this.bankId = bankObj.id;
	  		console.log(this.bankId)
	  	},
	  	confirmBuy () {
	  		let buyVersionsArray = _.map(this.versions,(item) => {
		  		return item.value;
		  	})
		  	 let reqParams = {
	        buyNumber: this.buyNum,
	        buyType: this.buyType.value,
	        period: this.period.value,
	        version: buyVersionsArray.join(',')
	      }
		  	 this.$http.get("https://www.easy-mock.com/mock/5b2f513b0e946a3379e72cab/banklist/buy",reqParams)
		  	 .then((res) => {
		  	 		this.orderId = res.body.createOrder.orderId;
		  	 		console.log(res.body.createOrder.orderId)
		  	 },(err) => {
		  	 	
		  	 })
	  		 this.isShowCheckOrder = true
         this.isShowPayDialog = false
	  	},
	  	hideCheckOrder(){
	  		 this.isShowCheckOrder = false;
	  	}
	  	
	  },
	  mounted:function(){
	  	this.buyNum = 1;
	  	this.buyType = this.buyTypes[0];
	  	this.versions = [this.versionList[0]]
	    this.period = this.periodList[0]
	    this.getPrice()
	  }
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.buy-dialog-title {
  font-size: 16px;
  font-weight: bold;
}
.buy-dialog-btn {
  margin-top: 20px;
}
.buy-dialog-table {
  width: 100%;
  margin-bottom: 20px;
}
.buy-dialog-table td,
.buy-dialog-table th{
  border: 1px solid #e3e3e3;
  text-align: center;
  padding: 5px 0;
}
.buy-dialog-table th {
  background: #4fc08d;
  color: #fff;
  border: 1px solid #4fc08d;
}
</style>
