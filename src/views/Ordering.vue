<template>
  <div id="ordering">
    <img class="examplePanel" src="@/assets/colorsplash.jpg">
    <section class="leftSection">
      <div id="menuButtons">
        <button id = "bigButtons" v-on:click="changeCategory(1); showBurger(true); showOrder(true)"><img src="@/assets/hamburger.png" width=100%></button>
          <div class="hamburgerIngredients" v-if="hamburgerButtons">
          <button :class="['menu-button', {'focused-category' : currentCategory === 1}]" v-on:click="changeCategory(1)"> {{ uiLabels.protein }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 2}]" v-on:click="changeCategory(2)"> {{ uiLabels.toppings }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 3}]" v-on:click="changeCategory(3)"> {{ uiLabels.sauce }} </button>
          <button :class="['menu-button', {'focused-category' : currentCategory === 4}]" v-on:click="changeCategory(4)"> {{ uiLabels.bread }} </button>
        </div>
          <button id = "bigButtons" v-on:click="changeCategory(5); showBurger(false); showOrder(true)"><img src="@/assets/fries.png" width=100%></button>
          <button id = "bigButtons" v-on:click="changeCategory(6); showBurger(false); showOrder(true)"><img src="@/assets/drink.png" width=100%></button>
      <div class="popupClass">
        <div v-if="redoBurgerBol">
          <button id="checkoutButton" v-on:click="redoPopUp()" > {{ uiLabels.checkout }} </button>
        </div>
        <div v-else>
          <button id="checkoutButton" v-on:click=" showOrder(false); showPlaceOrder(true);showBurger(false); redoBurgerBolFunc(false)"> {{ uiLabels.checkout }} </button>
        </div>
      </div>
    </div>
    </section>

    <section class="header">
      <h1> Crusty Burgers </h1>
    </section>

    <section class="middleSection" >
      <div id="welcomePage" v-if="buttonIsPressed == false">
        {{ uiLabels.welcome}} <br>
        <img src="@/assets/welcome.jpg" width=50% >
      </div>
      <div v-else>
        <div v-if="displayOrder">
        <h1 v-if="this.currentCategory==1">{{ uiLabels.protein}}</h1>
        <h1 v-if="this.currentCategory==2">{{ uiLabels.toppings}}</h1>
        <h1 v-if="this.currentCategory==3">{{ uiLabels.sauce}}</h1>
        <h1 v-if="this.currentCategory==4">{{ uiLabels.bread}}</h1>
        <h1 v-if="this.currentCategory==5">{{ uiLabels.extras}}</h1>
        <h1 v-if="this.currentCategory==6">{{ uiLabels.drinks}}</h1>
        <div v-if="this.currentCategory<=3" class="ingdiv">
          <Ingredient
            ref="ingredient"
            v-for= "(item,index)  in ingredients"
            v-show="item.category==currentCategory"
            v-on:increment="createBurger(item,index)"
            v-on:deincrement="removeIngredientNumber(item,index)"
            :item="item"
            :lang="lang"
            :thecounter="ingredientCounts[index]"
            :currentCategory=currentCategory
            :key="item.ingredient_id">
          </Ingredient>
        </div>
        <div v-else="this.currentCategory>=4" class="ingdiv2">
          <span>
          <Ingredient
          key="renderkey"
            ref="ingredient"
            v-for= "(item,index)  in ingredients"
            v-show="item.category==currentCategory"
            v-on:increment="createBurger(item,index)"
            v-on:deincrement="removeIngredientNumber(item,index)"
            :item="item"
            :lang="lang"
            :thecounter="ingredientCounts[index]"
            :currentCategory=currentCategory
            :key="item.ingredient_id">
          </Ingredient>
          </span>
        </div> <br> <br>
        </div>
        <div class="allPrevNextAdd">
          <div class="previousAndNext" v-if="hamburgerButtons">
            <div v-if="currentCategory >= 2">
              <button v-on:click="previousPage()" style="float: left;" ><img src="@/assets/backArrow.png" width = 100%>  {{ uiLabels.previous }}</button>
            </div>
            <div v-if="currentCategory <= 3">
              <button v-on:click="nextPage()" style="float: right;"><img src="@/assets/frontArrow.png" width = 100%> {{ uiLabels.next }}</button>
            </div>
          </div>
          <div id="addOrderButton" v-if="displayOrder">
              <button v-on:click="addButtonK(); showPlaceOrder(true); redoBurgerBolFunc(false)" style="float: right;"><img src="@/assets/cart.png" width = 50%><br>{{ uiLabels.addOrder }}</button>
          </div>
        </div>
        <div id="currentOrder" v-if="displayOrder == false">
          <h1>{{ uiLabels.yourOrder }}</h1>
          <div v-for="ab, index in outputOrderText">
            {{ ab}} &nbsp;
            <button v-on:click="removeItem(index)" id= "index" > X </button>
            <span  v-if="ab.includes('Burger')">
            <button v-on:click="changeBurger(index)" id="index2"> {{uiLabels.redoburger}}</button>
            </span>
            <br>
          </div>
          <div id="totalPrice" >
            {{uiLabels.totalPriceLang}} {{totalOrderPrice}} :-
          </div>
          <br>
          <br>
          <br>
        <div v-if="placeOrderBoolean">
          <button id="placeOrderButton" v-on:click="popupFunction()" style="float: right;"> {{uiLabels.placeOrder}} </button>
          <span class="popuptext" id="myPopup"> </span>
        </div>

      </div>
      </div>
    </section>


    <section class="rightSection">
      <button id="langButton" v-if="this.langBoolData" v-on:click="switchLang(); langBool(false)"><img src="@/assets/Sverige.png" width=100%></button>
      <button id="langButton" v-if="this.langBoolData==false" v-on:click="switchLang(); langBool(true)"><img src="@/assets/Storbritannien.png" width=100%></button>
      <div id="infoAllergy">
        <span id="milk"> L </span> = {{ uiLabels.contains }} {{ uiLabels.lactose }} <br>
        <span id="gluten">G</span> = {{ uiLabels.contains }} {{ uiLabels.gluten }} <br>
        <span id="vegan">V</span> = {{ uiLabels.vegan }}
      </div>
      <div class="rightInfo" v-if="hamburgerButtons">
        <div id="secondRightBox">
          <h1>{{ uiLabels.order }}</h1>
          <h4>{{uiLabels.protein}} </h4>
          <span v-for="ingri in this.burgerIngredients">
          <li v-if="ingri.category==1">{{ ingri["ingredient_"+lang] }}  </li>
        </span>
        <h4>{{ uiLabels.toppings }}</h4>
        <span v-for="ingri in this.burgerIngredients">
          <li v-if="ingri.category==2">{{ ingri["ingredient_"+lang] }}</li>
        </span>
        <h4>{{ uiLabels.sauce }}</h4>
        <span v-for="ingri in this.burgerIngredients">
        <li v-if="ingri.category==3">{{ ingri["ingredient_"+lang] }}  </li>
        </span>
        <h4>{{ uiLabels.bread }}</h4>
        <li v-if="isBreadIn()!==true">  {{uiLabels.breaddemand}} </li>
        <span  v-if="isBreadIn()" v-for="ingri in this.burgerIngredients">
          <li v-if="ingri.category==4">{{ ingri["ingredient_"+lang] }}  </li>
        </span>
        <h4>{{uiLabels.burgerprice}} {{ price }} kr </h4>
        </div>

    </div>
    <div class="hiding" v-else-if="currentCategory>=5">
      {{drinkprice}}
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
      drinkprice:0,
      ingredientsToSend:[],
      itemprice: 0,
      langBoolData:true,
      chosenIngredients: [],
      burgerIngredients: [],
      drinksAndExtras: [],
      outputOrderText: [],
      tempFoodObjekt:"",
      price: 0,
      totalOrderPrice: 0,
      ingredientCounts:[],
      orderNumber: "",
      currentCategory: 1,
      buttonIsPressed: false,
      hamburgerButtons: false,
      displayOrder: false,
      isburger: false,
      placeOrderBoolean: false,
      redoBurgerBol: false,
      aBurger: {
        bread: null,
        meat: [],
        additionals:[],
        sauce: [],
        price: 0
      },
      aDrinkOrExtra: {
        size: "Small",
        name: {},
        price: 0
      }
    }
  },
  created: function () {
    this.$store.state.socket.on('orderNumber', function (data) {
      this.orderNumber = data;
    }.bind(this));
  },
  methods: {
    createBurger: function (item,index) {
      if (this.currentCategory<=4){
        this.burgerIngredients.push(item);
        this.price += +item.selling_price;
        this.ingredientCounts[index]++
      }
      else if (this.currentCategory>=5) {
        this.drinksAndExtras.push(item);
        this.ingredientCounts[index]++
        this.drinkprice += +item.selling_price;
      }
    },

    addToOrder: function () {
      var breadbol;
      breadbol=this.isBreadIn();

      this.addToBurger();
      this.addToDrinkOrExtra();
      this.createOutputOrderText();
      this.ingredientCounts=Array(this.ingredients.length).fill(0);
      //this.chosenIngredients =  this.chosenIngredients.concat(this.burgerIngredients).concat(this.drinksAndExtras);
      this.burgerIngredients = [];
      this.drinksAndExtras = [];
      this.price=0;
    }
    ,

    addButtonK: function(){
      var breadBol
      breadBol=this.isBreadIn()
      if(this.isBreadIn() || this.burgerIngredients.length==0){
      this.addToOrder()
      this.showBurger(false)
      this.showOrder(false)
      }
      if(breadBol!==true && this.burgerIngredients.length>0) {
        window.alert( "Please choose a bread");
  }
    },

    redoChosen: function(theChosenIngredient){
      var i
      var j
      for(i=0; i<theChosenIngredient.length; i++){
        if(theChosenIngredient[i].bread != null){
          for(j=0; j<theChosenIngredient[i].meat.length; j++){
            this.ingredientsToSend.push(theChosenIngredient[i].meat[j])
            console.log("hej");
          }
          for(j=0; j<theChosenIngredient[i].additionals.length; j++){
            this.ingredientsToSend.push(theChosenIngredient[i].additionals[j])
          }
          for(j=0; j<theChosenIngredient[i].sauce.length; j++){
            this.ingredientsToSend.push(theChosenIngredient[i].sauce[j])
          }
          this.ingredientsToSend.push(theChosenIngredient[i].bread)
        }
        else {
          this.ingredientsToSend.push(theChosenIngredient[i].name)
        }
      }
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
        this.getPriceOfBurger(this.aBurger)
        this.chosenIngredients.push({bread:this.aBurger.bread, meat:this.aBurger.meat,
          additionals:this.aBurger.additionals, sauce:this.aBurger.sauce, price:this.itemprice});
          this.itemprice=0;
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
          this.getPriceOfBurger(this.drinksAndExtras[j])
          this.aDrinkOrExtra.name = this.drinksAndExtras[j]
          this.chosenIngredients.push({name:this.aDrinkOrExtra.name, size:this.aDrinkOrExtra.size, price:this.itemprice});
          this.itemprice=0;
          this.aDrinkOrExtra.name={};
          this.aDrinkOrExtra.size="Small";
        }
      }
    },

    redoBurgerIngredient: function(i){
     var j
      for(j=0; j<this.chosenIngredients[i].meat.length; j++){
        this.burgerIngredients.push(this.chosenIngredients[i].meat[j])
      }
      for(j=0; j<this.chosenIngredients[i].additionals.length; j++){
        this.burgerIngredients.push(this.chosenIngredients[i].additionals[j])
      }
      for(j=0; j<this.chosenIngredients[i].sauce.length; j++){
        this.burgerIngredients.push(this.chosenIngredients[i].sauce[j])
      }
      this.burgerIngredients.push(this.chosenIngredients[i].bread)
    },

    changeBurger: function(i){
      var j
      this.displayOrder=true;
      this.redoBurgerBol=true;
      this.redoBurgerIngredient(i);
      this.hamburgerButtons=true;
      this.currentCategory=1;
      this.chosenIngredients.splice(i,1);
      this.createOutputOrderText()
      this.displayOrder=true
      for (j= 0; j < this.burgerIngredients.length; j+= 1) {
        if(this.ingredients.indexOf(this.burgerIngredients[j])>-1){
        this.ingredientCounts[this.ingredients.indexOf(this.burgerIngredients[j])]++
      }
    }
    },

    placeOrder: function () {
      var i,
      //Wrap the order in an object
        order = {
          ingredients: this.ingredientsToSend,
          price: this.totalOrderPrice
        };
      // make use of socket.io's magic to send the stuff to the kitchen via the server (app.js)
      this.$store.state.socket.emit('order', {order: order});
      //set all counters to 0. Notice the use of $refs

      this.price = 0;
      this.totalOrderPrice = 0;
      this.chosenIngredients = [];
      this.ingredientsToSend= [];
      this.outputOrderText= [];
      this.ingredientCounts.fill(0);
      this.buttonIsPressed = false;
    },

    createOutputOrderText: function(){
      var i
      var j
      this.outputOrderText=[];
      this.totalOrderPrice=0;
      for (i=0; i <this.chosenIngredients.length; i++){
        if(this.chosenIngredients[i].bread != null){
          this.tempFoodObjekt= (i+1) +": Burger(" + this.chosenIngredients[i].bread["ingredient_" + this.lang]
          for (j=0; j < this.chosenIngredients[i].meat.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].meat[j]["ingredient_" + this.lang]
          }
          for (j=0; j < this.chosenIngredients[i].additionals.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].additionals[j]["ingredient_" + this.lang]
          }
          for (j=0; j < this.chosenIngredients[i].sauce.length; j++){
            this.tempFoodObjekt=this.tempFoodObjekt + ", " + this.chosenIngredients[i].sauce[j]["ingredient_" + this.lang]
          }
          this.tempFoodObjekt=this.tempFoodObjekt+", )" + this.chosenIngredients[i].price + ":-";
          this.outputOrderText.push(this.tempFoodObjekt);
          this.tempFoodObjekt = "";
          this.totalOrderPrice+=this.chosenIngredients[i].price
        }
        else {
          this.tempFoodObjekt= (i+1) + ": " + this.chosenIngredients[i].name["ingredient_" + this.lang] + ", "  + this.chosenIngredients[i].price + ":-";
          this.outputOrderText.push(this.tempFoodObjekt);
          this.tempFoodObjekt = "";

          this.totalOrderPrice+=this.chosenIngredients[i].price
        }
      }
    },

    changeCategory: function(int) {
      this.currentCategory = int;
    },

    showBurger: function(boolean) {
      this.hamburgerButtons = boolean;
      if(this.buttonIsPressed!=true){
      this.ingredientCounts=Array(this.ingredients.length).fill(0);
      this.buttonIsPressed=true;
    }
    },

    showOrder: function(boolean) {
      this.displayOrder = boolean;
    },

    langBool: function(boolean){
      this.langBoolData=boolean;
    },

    showPlaceOrder: function(boolean){
      this.placeOrderBoolean = boolean;
    },

    redoBurgerBolFunc: function(boolean){
      this.redoBurgerBol=boolean;
    },

    getPriceOfBurger: function(item){
      var j
        if(item.bread!=null){
        this.itemprice=parseInt(item.bread.selling_price);
        for (j=0; j < item.meat.length; j++){
          this.itemprice+=parseInt(item.meat[j].selling_price);
        }
        for (j=0; j < item.additionals.length; j++){
          this.itemprice+=parseInt(item.additionals[j].selling_price);
        }
        for (j=0; j < item.sauce.length; j++){
          this.itemprice+=parseInt(item.sauce[j].selling_price);
        }
      }
        else {
          this.itemprice=parseInt(item.selling_price);
        }
      },

    removeItem: function(index){
      this.chosenIngredients.splice(index,1);
      this.createOutputOrderText();
    },

    getBreadIndex: function(ingri){
      if(ingri.category==4){
        return ingri
      }
    },

    isBreadIn:function(){
      var i
      for(i=0; i <this.burgerIngredients.length; i++){

        if (this.burgerIngredients[i].category==4){
          return true
        }
      }
      return false
    },

    isNotBurger:function(){
        var i
        for(i=0; i <this.drinksAndExtras.length; i++){
          if (this.drinksAndExtras[i].category>4){
            return true
      }}
      return false
    },

    removeIngredientNumber: function(item,index){
      if (this.currentCategory <= 3){
        this.burgerIngredients.splice( this.burgerIngredients.indexOf(item),1);

        this.price += -item.selling_price;
      }
      else if (this.currentCategory == 4){
        var bread_index;
        if(this.isBreadIn()){
          bread_index=this.burgerIngredients.findIndex(this.getBreadIndex);
          this.price += -this.burgerIngredients[bread_index].selling_price;
          this.burgerIngredients.splice(bread_index,1);
          }
      }
      else {
        this.drinksAndExtras.splice( this.drinksAndExtras.indexOf(item),1);
        this.drinkprice += -item.selling_price;
      }
      this.ingredientCounts[index]--
    },

    nextPage: function(){
      this.currentCategory += 1;
    },

    previousPage: function(){
      this.currentCategory = this.currentCategory - 1;
    },

    popupFunction: function(){
      if (confirm("Are you sure you want to place the order?")){
        this.redoChosen(this.chosenIngredients)
        this.placeOrder()
      };
    },

    redoPopUp: function(){
      window.alert( "Please add the changed burger to your order");
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
  grid-template-rows: auto;
  margin: auto;
  width: 90%;
  font-family: "Comic Sans MS";
}

.leftSection{
  grid-column: 1;
  grid-row: 2;
  margin-bottom: 50%;
}

.header{
  column-rule-width: 100%;
  grid-column: 2;
  grid-row: 1;
  width: 100%;
  text-align: center;
}

.header h1{
  font-size: 350%;
  color: white;
  -webkit-text-fill-color: black;
  -webkit-text-stroke-width: 1.5px;
  -webkit-text-stroke-color: white;
}

.menu-button{
  font-size: 0.90em;
  font-family: "Comic Sans MS";
  border: 4px groove #ccd;
}

#bigButtons {
  background-color: white;
  border: 4px groove #ccd;
  display: inline-block;
}

.hamburgerIngredients {
  text-align: center;
}

.middleSection{
  grid-column: 2;
  grid-row: 2;
  border: 4px groove #ccd;
  background-color: white;
  margin-left: 5%;
  margin-right: 5%;
  margin-bottom: 50%;
  width:90%;
}

#welcomePage{
  font-size: 1.5em;
  text-align: center;
}

#totalPrice{
  font-size: 1.3em;
  border: 2px solid;
  padding-left: 10px;

  margin-top: 30px;
  float: right;
}

.allPrevNextAdd{
  display:grid;
  grid-template-columns: 50% 50%;
  grid-gap: 28.8%;
}

.previousAndNext{
  display: grid;
  grid-template-columns: 50% 50%;
  grid-gap: 1%;
  width: 50%;
  padding: 1%
}

.rightSection{
  grid-column: 3;
  grid-row: 2;
}

.rightSection button{
  margin-bottom: 30px;
  background-color: white;
  border: 4px groove #ccd;
  display: inline-block;
}

.ingdiv{
  overflow-y:scroll;
  height:50vh;
  box-shadow: 0px 12px 5px -2px lightgray;
}

.rightInfo {
  margin-top: 30px;
  border: 4px groove #ccd;
  background-color: white;
}

#menuButtons{
  display: grid;
  grid-gap: 3%;
  width: 100%;
    grid-template-columns: repeat(1, 1fr);
}

#placeOrderButton{
  font-size: 1.78em;
  font-family: "Comic Sans MS";
  background-color: LightSkyBlue;
  border: 2px solid;
}

#addOrderButton {
  display: grid;
  font-size: 100%;
  width: 40%;
  padding:1%;
}

#checkoutButton {
  width: 100%;
  font-size: 1.4em;
  font-family: "Comic Sans MS";
  border: 4px groove #ccd;
}

#infoAllergy {
  border: 4px groove #ccd;
  padding: 1em;
  background-color: white;
  width: 80%;
}

#secondRightBox {
  padding: 1em;
}

.hiding{
  opacity: 0;
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

#index{
  background-color: OrangeRed;
  font-size: 0.9em;
  font-weight: bold;
  border: 2px solid;
  height:0.95;
}

#index2{
  background-color: Orange;
  margin-left: 1%;
  font-size: 0.9em;
  font-weight: bold;
  border: 2px solid;
  height:0.95;
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
