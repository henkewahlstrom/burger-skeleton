<template>
<div id="orders">
  <section class="leftSection">
  <h1>{{ uiLabels.ordersInQueue }}</h1>
  <div>
    <OrderItemToPrepare
      v-for="(order, key) in orders"
      v-if="order.status !== 'done'"
      v-on:done="markDone(key)"
      :order-id="key"
      :order="order"
      :ui-labels="uiLabels"
      :lang="lang"
      :burgerordrink="doburger"
      :key="key">
    </OrderItemToPrepare>
  </div>
</section>

<section class="middleSection">
  <h1>{{ uiLabels.drinksAndExtras }}</h1>

  <div>
    <OrderItemToPrepare
      v-for="(order, key) in orders"
      v-if="order.status !== 'done'"
      v-on:done="markDone(key)"
      :order-id="key"
      :order="order"
      :ui-labels="uiLabels"
      :lang="lang"
      :burgerordrink="dodrink"
      :key="key">
    </OrderItemToPrepare>

  </div>
</section>

<section class="rightSection">
  <h1>{{ uiLabels.ordersFinished }}</h1>
  <OrderItemToPrepare
  v-for="(order, key) in orders"
  v-if="order.status === 'done'"
  :order-id="key"
  :order="order"
  :ui-labels="uiLabels"
  :lang="lang"
  :burgerordrink="dodrink"
  :key="key">
</OrderItemToPrepare>


</section>
</div>
</template>
<script>
import OrderItem from '@/components/OrderItem.vue'
import OrderItemToPrepare from '@/components/OrderItemToPrepare.vue'

//import methods and data that are shared between ordering and kitchen views
import sharedVueStuff from '@/components/sharedVueStuff.js'

export default {
  name: 'Ordering',
  components: {
    OrderItem,
    OrderItemToPrepare
  },
  mixins: [sharedVueStuff], // include stuff that is used in both
                            //the ordering system and the kitchen
  data: function(){
    return {
      chosenIngredients: [],
      price: 0,
      doburger: true,
      dodrink: false
    }
  },
  methods: {
    markDone: function (orderid) {
      this.$store.state.socket.emit("orderDone", orderid);
    }
  }
}
</script>
<style scoped>
    #orders {
    font-size:24pt;
    display: grid;
    grid-gap: 5px;
    grid-template-columns: 35% 35% 30%;
    margin: 40px;
  }
  .leftSection{
    grid-column: 1;
    background-color: orange;
  }
  .middleSection {
    grid-column: 2;
    background-color: blue;
  }
  .rightSection{
    grid-column: 3;
    background-color: green;
  }

  h1 {
    text-transform: uppercase;
    font-size: 1.4em;
  }
</style>
