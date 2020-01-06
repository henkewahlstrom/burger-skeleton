<template>
  <!-- Note in this component that it is using another component -->
<div>
  <span v-if="this.run"> <span v-if="divideorder()"> </span></span>
  <span v-if="itemscreen">
  <OrderItem
    v-if="burgerordrink"
    :ui-labels="uiLabels"
    :lang="lang"
    :order-id="orderId"
    :itemscreen="itemscreen"
    :burgers="this.burgers"
    :burgerordrink="burgerordrink"
    :drinksAndExtras="this.drinksAndExtras">
  </OrderItem>
  <OrderItem
    v-if="burgerordrink!==true"
    :ui-labels="uiLabels"
    :lang="lang"
    :order-id="orderId"
    :burgers="this.burgers"
    :itemscreen="itemscreen"
    :drinksAndExtras="this.drinksAndExtras"
    :burgerordrink="burgerordrink">
  </OrderItem>
  </span>

  <OrderItem
    v-if="itemscreen!==true"
    :ui-labels="uiLabels"
    :lang="lang"
    :order-id="orderId"
    :burgers="this.burgers"
    :itemscreen="itemscreen"
    :drinksAndExtras="this.drinksAndExtras"
    :burgerordrink="burgerordrink">
  </OrderItem>
  <span  v-if="status!==true">
  <button id="doneButton"  v-if="itemscreen!==true" v-on:click="orderDone">
    {{uiLabels.ready}}
  </button>
  <br>
</span>

<!--  <span v-if="burgerordrink!==true">
    <button v-on:click="holddrink()">drink</button>
  <button v-if="this.burgerdone" v-on:click="orderDone">
    {{uiLabels.ready}}
  </button>
</span>
  <span v-if="burgerordrink">
    <button v-on:click="holdburger()">burger done</button>
  <button v-if="this.burgerdone "v-on:click="orderDone">
    {{uiLabels.ready}}
  </button>
  </span>
-->
</div>
</template>
<script>
import OrderItem from '@/components/OrderItem.vue'

export default {
  name: 'OrderItemToPrepare',
  components: { OrderItem },


  props: {
    uiLabels: Object,
    order: Object,
    orderId: String,
    lang: String,
    burgerordrink:Boolean,
    itemscreen:Boolean,
    status:Boolean
  },
  data: function(){
    return{
      drinksAndExtras:[],
      burgers:[],
      aBurger:[],
      divide:true,
      run:true,
      drinkdone:false,
      burgerdone:false
    }
  },
  methods: {
    orderDone: function () {
      // sending 'done' message to parent component or view so that it
      // can catch it with v-on:done in the component declaration
      this.$emit('done');
    },
    cancelOrder: function () {
      // not implemented
    },

    holdburger:function(){
      this.burgerdone=true
    },

    holddrink:function(){
      this.drinkdone=true
    },


    divideorder: function(){
      var i
      for (i=0;i<this.order.ingredients.length;i++){
        if (this.order.ingredients[i].category < 4){
          this.aBurger.push(this.order.ingredients[i])
        }
        else if (this.order.ingredients[i].category == 4){
          this.aBurger.push(this.order.ingredients[i])
          this.burgers.push(this.aBurger);
          this.aBurger=[];
        }
        else{
          this.drinksAndExtras.push(this.order.ingredients[i])
        }
      }
      this.run=false
      return true


    }

  }

}
</script>
<style scoped>
div {
  background-color: lightgray;
  color: black;
  padding: 1%;
  margin: 1%;
  border-style: double;
  border-color: black;
  border-radius: 3%;
  min-height: 20vh;
}

#doneButton{
  width: 100%;
  display: inline-block;
  border: 2px groove #ccd;
  border-radius: 3%;
  text-align: center;
  background-color: Lightgreen;
}
#doneButton:hover{
  background-color: green;
  cursor: pointer;
}
</style>
