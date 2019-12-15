<template>
<div id="orders">
  <section class="middleSection">
  <h1>BURGERS</h1>
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
      :status="false"
      :key="key"
      :itemscreen="true">
    </OrderItemToPrepare>
  </div>
</section>

<section class="rightSection">
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
          :status="false"
          :itemscreen="true"
          :key="key">
        </OrderItemToPrepare>

      </div>
    </section>

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
        :burgerordrink="dodrink"
        :status="false"
        :itemscreen="false"
        :key="key">
      </OrderItemToPrepare>
      </div>
    </section>

    <section class="mostRightSection">
      <h1>{{ uiLabels.ordersFinished }}</h1>
      <div>
        <OrderItemToPrepare
        v-for="(order, key) in orders"
        v-if="order.status === 'done'"
        :order-id="key"
        :order="order"
        :ui-labels="uiLabels"
        :lang="lang"
        :status="true"
        :burgerordrink="dodrink"
        :itemscreen="false"
        :key="key">
        </OrderItemToPrepare>
      </div>
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
    font-size:18pt;
    display: grid;
    grid-gap: 5px;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-rows: auto;
    margin: 40px;
  }
  #orders section{
    min-height: 90vh;
  }
  .leftSection{
    grid-column: 1;
    grid-row: 1;
    background-color: orange;
  }
  .middleSection {
    grid-column: 2;
    grid-row: 1;
    background-color: LightSkyBlue;
  }
  .rightSection{
    grid-column:3;
    grid-row: 1;
    background-color: LightSkyBlue;
  }
  .mostRightSection{
    grid-column: 4;
    grid-row: 1;
    background-color: green;
  }

  h1 {
    text-transform: uppercase;
    font-size: 1.4em;
  }
</style>
