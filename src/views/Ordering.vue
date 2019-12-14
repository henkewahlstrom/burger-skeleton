<template>
  <div id="ordering">
    <img class="examplePanel" src="@/assets/colorsplash.jpg">
    <section class="leftSection">
      <div id="menuButtons">
        <button id = "bigButtons" v-on:click="changeCategory(1); showBurger(true); showOrder(true)"><img src="@/assets/hamburger.png" width=200px></button>

        <div class="hamburgerIngredients" v-if="hamburgerButtons">
          <button :class="['menu-button', {'focused-category' : currentCategory === 1}]" v-on:click="changeCategory(1)"> {{ uiLabels.protein }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 2}]" v-on:click="changeCategory(2)"> {{ uiLabels.toppings }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 3}]" v-on:click="changeCategory(3)"> {{ uiLabels.sauce }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 4}]" v-on:click="changeCategory(4)"> {{ uiLabels.bread }} </button>
        </div>
          <button id = "bigButtons" v-on:click="changeCategory(5); showBurger(false); showOrder(true)"><img src="@/assets/fries.png" width=200px></button>
          <button id = "bigButtons" v-on:click="changeCategory(6); showBurger(false); showOrder(true)"><img src="@/assets/drink.png" width=200px></button>
      <div class="popupClass">
        <button id="checkoutButton" v-on:click="popupFunction();"> {{ uiLabels.checkout }} {{totalOrderPrice}} kr </button>
        <span class="popuptext" id="myPopup"> </span>
      </div>
    </div>
    </section>

    <section class="header">
      <h1> Crusty Burgers </h1>
    </section>

    <section class="middleSection" >
      <div v-if="displayOrder" class="ingdiv">
        <h1>{{ uiLabels.ingredients }}</h1>
        <Ingredient
          ref="ingredient"
          v-for= "item  in ingredients"
          v-show="item.category==currentCategory"
          v-on:increment="createBurger(item)"
          v-on:deincrement="removeIngredientNumber(item)"
          :item="item"
          :lang="lang"
          :currentCategory=currentCategory
          :key="item.ingredient_id">
        </Ingredient>
      </div> <br> <br>
        <div v-if="hamburgerButtons">
          <div v-if="currentCategory >= 2">
            <button v-on:click="previousPage()" style="float: left;"><img src="@/assets/backArrow.png" width = 40> {{ uiLabels.previous }}</button>
          </div>
          <div v-if="currentCategory <= 3">
            <button v-on:click="nextPage()" style="float: left;"><img src="@/assets/frontArrow.png" width = 40> {{ uiLabels.next }}</button>
          </div>
        </div>
        <div id="addOrderButton" v-if="displayOrder">
            <button v-on:click="addToOrder(); showBurger(false); showOrder(false)" style="float: right;"><img src="@/assets/cart.png" width = 40> {{ uiLabels.addOrder }}</button>
        </div>
      <div v-if="displayOrder == false">
        <h1>{{ uiLabels.yourOrder }}</h1>
        <div v-for="ab, index in outputOrderText">
          {{ ab}}
          <button v-on:click="removeItem(index)" id= "index" > delete </button>
          <br>
        </div>
      </div>
      </section>


    <section class="rightSection">
      <div id="infoAllergy">
        <span id="milk"> L </span> = {{ uiLabels.contains }} {{ uiLabels.lactose }} <br>
        <span id="gluten">G</span> = {{ uiLabels.contains }} {{ uiLabels.gluten }} <br>
        <span id="vegan">V</span> = {{ uiLabels.vegan }}
      </div>
      <div class="rightInfo">
        <div id="secondRightBox">
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
      </div>
    </div>
    <button v-on:click="switchLang()"><img src={{ uiLabels.language }}>{{ uiLabels.language }}</button>
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
      totalOrderPrice: 0,
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
          ingredients: this.outputOrderText,
          price: this.price
        };
      // make use of socket.io's magic to send the stuff to the kitchen via the server (app.js)
      this.$store.state.socket.emit('order', {order: order});
      //set all counters to 0. Notice the use of $refs
      for (i = 0; i < this.$refs.ingredient.length; i += 1) {
        this.$refs.ingredient[i].resetCounter();
      }
      this.price = 0;
      this.totalOrderPrice = 0;
      this.chosenIngredients = [];
    },

    createOutputOrderText: function(){
      var i
      var j
      this.outputOrderText=[];
      for (i=0; i <this.chosenIngredients.length; i++){
        if(this.chosenIngredients[i].bread != null){
          this.tempFoodObjekt= (i+1) +": " + this.chosenIngredients[i].bread["ingredient_" + this.lang]
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
          this.tempFoodObjekt= (i+1) + ": " + this.chosenIngredients[i].name["ingredient_" + this.lang] + ", " + this.chosenIngredients[i].size
          this.outputOrderText.push(this.tempFoodObjekt);
          this.tempFoodObjekt = "";
        }
      this.totalOrderPrice=this.price;
      }
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
      console.log(this.chosenIngredients[index])
      this.createOutputOrderText();
    },

    getbreadindex: function(ingri){
      if(ingri.category==4){
        return ingri
      }
    },

      isbreadin:function(){
        for(ing in burgerIngredients){
          console.log(ing.catagory)
          if (ing.catagory==4){
            return true
          }
        }
        return false
      },



    removeIngredientNumber: function(item){
      if (this.currentCategory <= 3){
        this.burgerIngredients.splice( this.burgerIngredients.indexOf(item),1);

        this.price += -item.selling_price;
      }
      else if (this.currentCategory == 4){
        var bread_index;
        console.log("tja")
        if(this.isbreadin){
          console.log("hej")
          bread_index=this.burgerIngredients.findIndex(this.getbreadindex);
          this.price += -this.burgerIngredients[bread_index].selling_price;
          this.burgerIngredients.splice(bread_index,1);

          }
      }
      else {
        this.drinksAndExtras.splice( this.drinksAndExtras.indexOf(item),1);
        this.price += -item.selling_price;
      }
    },
    nextPage: function(){
      this.currentCategory += 1;
    },
    previousPage: function(){
      this.currentCategory = this.currentCategory - 1;
    },

    popupFunction: function(){
      if (confirm("Place order")){
        this.placeOrder()
      };
    }

  }
}
</script>
<style scoped>
/* scoped in the style tag means that these rules will only apply to elements, classes and ids in this template and no other templates. */
@media all and (max-width: 700px) {
  .examplePanel {
    position: fixed;
    background-size: cover;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    z-index: -2;
  }
}
.examplePanel {
  position: fixed;
  background-size: cover;
  width: 100%;
  left: 0;
  top: 0;
  z-index: -2;
}

#ordering {
  display: grid;
  grid-gap: 15px;
    grid-template-columns: 20% 60% 20%;
  margin: auto;
  width: 90%;
  min-width: 1000px;
  font-family: "Comic Sans MS";
}
.leftSection{
  grid-column: 1;
  grid-row: 1 / span 3;
  margin-top: 150px
}

.header{
  grid-column: 2;
  grid-row: 1;
  font-size: 3.5em;
  color: white;
  -webkit-text-fill-color: black;
  -webkit-text-stroke-width: 1.5px;
  -webkit-text-stroke-color: white;
  margin-top: -50px;
  text-align: center;
}

.menu-button{
  font-size: 0.94em;
  border: 4px groove #ccd;
}

#bigButtons {
  border: 4px groove #ccd;
}

.middleSection{
  grid-column: 2;
  grid-row: 2;
  border: 4px groove #ccd;
  background-color: white;
  margin-left: 15px;
  padding: 1em;
  width:700px;
  margin-top: -73px;
}

.rightSection{
  grid-column: 3;
  grid-row: 1 / span 3;
  margin-top: 135px;
  padding: 1em;
}

.ingdiv{
  overflow-y:scroll;
  height:50vh;
  box-shadow: 0px 12px 5px -2px lightgray
}

.rightInfo {
  margin-top: 30px;
  border: 4px groove #ccd;
  background-color: white;
}

#menuButtons{
  display: grid;
  grid-gap: 20px;
    grid-template-columns: repeat(1, 1fr);
}

#addOrderButton {
  font-size: 50em;
}
#checkoutButton {
  width: 270px;
  height: 80px;
  margin-top: 10px;
  font-size: 18px;
  font-family: "Comic Sans MS";
  border: 4px groove #ccd;
}

#infoAllergy {
  border: 4px groove #ccd;
  padding: 1em;
  background-color: white;
  width: 198px;

}

#secondRightBox {
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
.focused-category {
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
  background-color: white;
}

</style>
