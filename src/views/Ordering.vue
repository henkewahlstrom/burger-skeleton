<template>
  <div id="ordering">
    <section class="leftSection">
      <div id="menuButtons">
        <button v-on:click="switchLang()">{{ uiLabels.language }}</button>
        <button v-on:click="changeCategory(1); showBurger(true); showOrder(true)"><img src="@/assets/hamburger.png" width=200></button>

        <div class="hamburgerIngredients" v-if="hamburgerButtons">
          <button v-on:click="changeCategory(1)"> {{ uiLabels.protein }} </button>
          <button v-on:click="changeCategory(2)"> {{ uiLabels.toppings }} </button>
          <button v-on:click="changeCategory(3)"> {{ uiLabels.sauce }} </button>
          <button v-on:click="changeCategory(4)"> {{ uiLabels.bread }} </button>
        </div>
          <button v-on:click="changeCategory(5); showBurger(false); showOrder(true)"><img src="@/assets/fries.png" width=200></button>
          <button v-on:click="changeCategory(6); showBurger(false); showOrder(true)"><img src="@/assets/drink.png" width=200></button>
      </div>
    </section>

    <section class="middleSection" >
      <div v-if="displayOrder">
        <h1>{{ uiLabels.ingredients }}</h1>
        <Ingredient
          ref="ingredient"
          v-for= "item  in ingredients"
          v-show="item.category==currentCategory"
          v-on:increment="createBurger(item)"
          v-on:deincrement="removeIngredientNumber(item)"
          :item="item"
          :lang="lang"
          :key="item.ingredient_id">
        </Ingredient>
        <div v-if="hamburgerButtons">
          <div v-if="currentCategory >= 2">
            <button v-on:click="previousPage()" style="float: left;"><img src="@/assets/backArrow.png" width = 40> {{ uiLabels.previous }}</button>
          </div>
          <div v-if="currentCategory <= 3">
            <button v-on:click="nextPage()" style="float: left;"><img src="@/assets/frontArrow.png" width = 40> {{ uiLabels.next }}</button>
          </div>
        </div>
        <div id="addOrderButton">
            <button v-on:click="addToOrder(); showBurger(false); showOrder(false)" style="float: right;"><img src="@/assets/cart.png" width = 40> {{ uiLabels.addOrder }}</button>
        </div>
      </div>

      <div v-if="displayOrder == false">
        <h1>{{ uiLabels.yourOrder }}</h1>
        <div v-for="ab, index in outputOrderText">
          {{ ab}}
          <button v-on:click="removeItem(index)" id= index > delete </button>
          <br>

        </div>
        <button v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>
      </div>
    </section>
    <section class="rightSection">
      <div id="infoAllergy">
        <span id="milk"> L </span> = {{ uiLabels.contains }} {{ uiLabels.lactose }} <br>
        <span id="gluten">G</span> = {{ uiLabels.contains }} {{ uiLabels.gluten }} <br>
        <span id="vegan">V</span> = {{ uiLabels.vegan }}
      </div>
      <div v-if="hamburgerButtons">
        <h1>{{ uiLabels.order }}</h1>
        {{ burgerIngredients.map(item => item["ingredient_"+lang]).join(', ') }}, {{ price }} kr <br>
      </div>
      <h1>{{ uiLabels.ordersInQueue }}</h1>
      <div>
        <OrderItem
          v-for="(order, key) in orders"
          v-if="order.status !== 'done'"
          :order-id="key"
          :order="order"
          :ui-labels="uiLabels"
          :lang="lang"
          :key="key">
        </OrderItem>
      </div>
    </section>
  </div>
</template>
<script>

//import the components that are used in the template, the name that you
//use for importing will be used in the template above and also below in
//components
import Ingredient from '@/components/Ingredient.vue'
import OrderItem from '@/components/OrderItem.vue'

//import methods and data that are shared between ordering and kitchen views
import sharedVueStuff from '@/components/sharedVueStuff.js'

/* instead of defining a Vue instance, export default allows the only
necessary Vue instance (found in main.js) to import your data and methods */
export default {
  name: 'Ordering',
  components: {
    Ingredient,
    OrderItem
  },
  mixins: [sharedVueStuff], // include stuff that is used in both
                            // the ordering system and the kitchen
  data: function() { //Not that data is a function!
    return {
      chosenIngredients: [],
      burgerIngredients: [],
      drinksAndExtras: [],
      outputOrderText: [],
      tempFoodObjekt:"",
      price: 0,
      orderNumber: "",
      currentCategory: 1,
      hamburgerButtons: false,
      displayOrder: false,
      isburger: false,
      aBurger: {
        bread: null,
        meat: [],
        additionals:[],
        sauce: []
      },
      aDrinkOrExtra: {
        size: "Small",
        name: {}
      }
    }
  },
  created: function () {
    this.$store.state.socket.on('orderNumber', function (data) {
      this.orderNumber = data;
    }.bind(this));
  },
  methods: {
    createBurger: function (item) {
      if (this.currentCategory<=4){
        this.burgerIngredients.push(item);
        this.price += +item.selling_price;
      }
      else if (this.currentCategory>=5) {
        this.drinksAndExtras.push(item);
        this.price += +item.selling_price;
      }

    },
    addToOrder: function () {
      this.addToBurger();
      this.addToDrinkOrExtra();
      this.createOutputOrderText();
      //this.chosenIngredients =  this.chosenIngredients.concat(this.burgerIngredients).concat(this.drinksAndExtras);
      this.burgerIngredients = [];
      this.drinksAndExtras = []
    },
    addToBurger: function(){
      var i;
      for (i=0; i < this.burgerIngredients.length; i++){
      if (this.burgerIngredients[i].category == 1) {
      this.aBurger.meat.push(this.burgerIngredients[i]);
      }
      else if (this.burgerIngredients[i].category == 2) {
        this.aBurger.additionals.push(this.burgerIngredients[i]);
      }
      else if (this.burgerIngredients[i].category == 3) {
        this.aBurger.sauce.push(this.burgerIngredients[i]);
      }
      else if (this.burgerIngredients[i].category == 4)
       { this.isBurger=true;
        this.aBurger.bread = this.burgerIngredients[i];
        }
      }
      if(this.isBurger){
        this.chosenIngredients.push({bread:this.aBurger.bread, meat:this.aBurger.meat,
          additionals:this.aBurger.additionals, sauce:this.aBurger.sauce});
          this.aBurger.bread=null;
          this.aBurger.meat=[];
          this.aBurger.additionals=[];
          this.aBurger.sauce=[]
        this.isBurger=false;
      }
      //this.chosenIngredients.push(this.aBurger);
    },
    addToDrinkOrExtra: function(){
      var j;
      for (j=0; j< this.drinksAndExtras.length; j++){
        if (this.drinksAndExtras[j].category >= 5){
          this.aDrinkOrExtra.name = this.drinksAndExtras[j]
          this.chosenIngredients.push({name:this.aDrinkOrExtra.name, size:this.aDrinkOrExtra.size});
          this.aDrinkOrExtra.name={};
          this.aDrinkOrExtra.size="Small";
        }
      }
    },
    placeOrder: function () {
      var i,
      //Wrap the order in an object
        order = {
          ingredients: this.chosenIngredients,
          price: this.price
        };
      // make use of socket.io's magic to send the stuff to the kitchen via the server (app.js)
      this.$store.state.socket.emit('order', {order: order});
      //set all counters to 0. Notice the use of $refs
      for (i = 0; i < this.$refs.ingredient.length; i += 1) {
        this.$refs.ingredient[i].resetCounter();
      }
      this.price = 0;
      this.chosenIngredients = [];
    },

    createOutputOrderText: function(){
      var i
      var j
      this.outputOrderText=[];
      for (i=0; i <this.chosenIngredients.length; i++){
        if(this.chosenIngredients[i].bread != null){
          this.tempFoodObjekt= i +": " + this.chosenIngredients[i].bread["ingredient_" + this.lang]
          for (j=0; j < this.chosenIngredients[i].meat.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].meat[j]["ingredient_" + this.lang]
          }
          for (j=0; j < this.chosenIngredients[i].additionals.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].additionals[j]["ingredient_" + this.lang]
          }
          for (j=0; j < this.chosenIngredients[i].sauce.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].sauce[j]["ingredient_" + this.lang]
          }
          this.outputOrderText.push(this.tempFoodObjekt);
          this.tempFoodObjekt = "";
        }
        else {
          console.log(this.chosenIngredients)
          console.log(this.chosenIngredients[i].name["ingredient_" + this.lang])
          this.tempFoodObjekt=this.chosenIngredients[i].name["ingredient_" + this.lang] + ", " + this.chosenIngredients[i].size
          this.outputOrderText.push(this.tempFoodObjekt);
          this.tempFoodObjekt = "";
        }
      }
      console.log(this.outputOrderText);
    },
    changeCategory: function(int) {
      this.currentCategory = int;
    },

    showBurger: function(boolean) {
      this.hamburgerButtons = boolean;
    },
    showOrder: function(boolean) {
      this.displayOrder = boolean;
    },
    removeItem: function(index){
      this.chosenIngredients.splice(index,1);
      this.createOutputOrderText();
    },
    removeIngredientNumber: function(item){
      if (this.currentCategory <= 4){
        this.burgerIngredients.pop();
        this.price += -item.selling_price;
      }
      else {
        this.drinksAndExtras.pop();
        this.price += -item.selling_price;
      }
    },
    nextPage: function(){
      this.currentCategory += 1;
    },
    previousPage: function(){
      this.currentCategory = this.currentCategory - 1;
    }

  }
}
</script>
<style scoped>
/* scoped in the style tag means that these rules will only apply to elements, classes and ids in this template and no other templates. */
#ordering {
  display: grid;
  grid-gap: 5px;
    grid-template-columns: 20% 45% 35%;
  margin-left: 40px;
}
.leftSection{
  grid-column: 1;
}
.middleSection{
  grid-column: 2;
}
.rightSection{
  grid-column: 3;
}
#menuButtons{
  display: grid;
  grid-gap: 2px;
    grid-template-columns: repeat(1, 1fr);
}

#menuButtons button{
  width: 100%;
}

#addOrderButton {
  font-size: 50em;
}

#infoAllergy {
  border: 1px solid #ccd;
  padding: 1em;

}
#milk {
  color: blue;
}

#gluten {
  color: brown;
}

#vegan {
  color: green;
}
.hamburgerIngredients button:focus {
  background-color: black;
  color: white;
}

.example-panel {
  position: fixed;
  left:0;
  top:0;
  z-index: -2;
}
.ingredient {
  border: 1px solid #ccd;
  padding: 1em;
}
</style>
