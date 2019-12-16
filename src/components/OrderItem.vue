<template >
	<div class="orderBox" v-if="itemscreen">
    <div v-if="burgerordrink" >
			<b><i id="orderID">#{{orderId}}:</i></b>
			<span v-for="(burger, index) in burgers">
				<ul><li>{{index+1}}:</li></ul>
				 <span v-for="ing in burger"><ul id="burgerIngredients"><li>- {{ing["ingredient_"+ lang]}}</li></ul></span>
			</span>
		</div>
    <div v-else-if="burgerordrink!==true" >
			<b><i id="orderID">#{{orderId}}:</i></b>
			<span v-for="drink in drinksAndExtras">
				<ul id="burgerIngredients"><li>- {{drink["ingredient_"+ lang]}}</li></ul>
			</span>
    </div>
	</div>
	<div class="orderBox" v-else-if="itemscreen!==true">
			<b><i id="orderID">#{{orderId}}:</i></b><br>
			<b>Burgers:</b>
			<span v-for="(burger ,index) in burgers">
          <ul><li>{{index+1}}:</li></ul>
				 <span v-for="ing in burger"><ul id="burgerIngredients"><li>- {{ing["ingredient_"+ lang]}} </li></ul></span>
			</span>
			<b>Drinks:</b>
				 <span v-for="(drink ,index) in drinksAndExtras">
      		<ul id="burgerIngredients"><li>{{drink["ingredient_"+ lang]}}</li></ul></span>
	</div>

</template>
<script>
export default {
  name: 'OrderItem',
  props: {
    uiLabels: Object,
		drinksAndExtras:Array,
		burgers:Array,
    orderId: String,
    lang: String,
    burgerordrink:Boolean,
		itemscreen:Boolean
  }
}
</script>
<style scoped>
.orderBox{
	overflow-y: auto;
	overflow-x: auto;
	max-height: 20vh;
	min-height: 20vh;
}
#burgerIngredients {
	list-style-type: none;
	line-height: 20%;
}
#orderID{
	font-size: 1.2em;
}
div{
	line-height: 80%;
}
div span{
	line-height: 20%;
}

::-webkit-scrollbar {
  width: 6px;
	border-radius: 15%;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
  box-shadow: inset 0 0 5px grey;
  border-radius: 15%;
}

::-webkit-scrollbar-thumb {
  background: #888;
	border-radius: 15%;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>
