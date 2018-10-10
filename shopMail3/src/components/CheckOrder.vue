<template>
	<div>
		 <this-dialog :is-show="isShowCheckDialog" @on-close="checkStatus">
		      请检查你的支付状态！
		      <div class="button" @click="checkStatus">
		    	    支付成功
		      </div>
		      <div class="button" @click="checkStatus">
		       	 支付失败
		      </div>
   		</this-dialog>
   		 <this-dialog :is-show="isShowSuccessDialog" @on-close="hideSuccessDialog">
      		购买成功！
   		 </this-dialog>
    	<this-dialog :is-show="isShowFailDialog" @on-close="hideFailDialog">
      		购买失败！
    	</this-dialog>
	</div>
</template>
<script>
	import Dialog from './dialog'
	export default {
		components:{
			thisDialog:Dialog
		},
		props:{
			isShowCheckDialog:{
				type:Boolean,
				dedault:false
			},
			orderId:{
				 type: [String, Number]
			}
		},
		data () {
			return {
				 isShowSuccessDialog: false,
      			isShowFailDialog: false
			}
		},
		methods:{
			checkStatus(){
				 this.$http.get("https://www.easy-mock.com/mock/5b2f513b0e946a3379e72cab/banklist/buy",{orderId:this.orderId})
			  	 .then((res) => {
			  	 		 this.isShowSuccessDialog = true
        				this.$emit('on-close-check-dialog')
			  	 		//console.log(res.body.createOrder.orderId)
			  	 },(err) => {
			  	 	 this.isShowFailDialog = true
        			 this.$emit('on-close-check-dialog')
			  	 })
			},
			hideSuccessDialog(){
				this.isShowSuccessDialog = false;
			},
			hideFailDialog(){
				this.isShowFailDialog = false;
			}
		}
	}
</script>
<style scoped>
	
</style>