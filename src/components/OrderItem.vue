<template>
    <div v-if="burgerordrink" >
			<span v-for="(burger ,index) in order">
        {{orderId}} burger: {{index}}{{uiLabels.ingredients}}:
				 <span v-for="ing in burger"> {{ing["ingredient_"+ lang]}}, </span>
				 <br>
				 </span>
				 </div>
    <div v-else-if="burgerordrink!==true" >
			<span v-for="(drink ,index) in order">
        {{orderId}} aDrinkOrExtra {{index}} {{drink["ingredient_"+ lang]}}</span>
    </div>
</template>
<script>
export default {
  name: 'OrderItem',
  props: {
    uiLabels: Object,
    order: Array,
    orderId: String,
    lang: String,
    burgerordrink:Boolean
  }
}
</script>
<style scoped>

</style>
