<template>
	<div v-if="itemscreen">
    <div v-if="burgerordrink" >
			<span v-for="(burger ,index) in burgers">
        {{orderId}} burger: {{index+1}}{{uiLabels.ingredients}}:
				 <span v-for="ing in burger"> {{ing["ingredient_"+ lang]}}, </span>
				 <br>
				 </span>
				 </div>
    <div v-else-if="burgerordrink!==true" >
			<span v-for="(drink ,index) in drinksAndExtras">
        {{orderId}} aDrinkOrExtra {{index +1}} {{drink["ingredient_"+ lang]}}</span>
    </div>
	</div>
	<div v-else-if="itemscreen!==true">
    <div>
			{{orderId}}
			<span v-for="(burger ,index) in burgers">
         burger: {{index}}{{uiLabels.ingredients}}:
				 <span v-for="ing in burger"> {{ing["ingredient_"+ lang]}}, </span>
				 <br>
				 </span>
				 </div>
    <div >
			<span v-for="(drink ,index) in drinksAndExtras">
      {{index}} {{drink["ingredient_"+ lang]}}</span>
    </div>
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

</style>
