<template>
  <div class="ingredient">
    <div v-if="item.category != 4">
    <label>
      <span class="ingrtext">
      <button v-on:click="incrementDeCounter"> - </button>
      {{thecounter}}
      <button v-on:click="incrementCounter"> + </button>
    </span>
      {{item["ingredient_"+ lang]}}, {{item.selling_price}}:-
      <span  id="milk" v-if="item.milk_free == 0" style="float: right;"> &nbsp; &nbsp; L </span>
      <span  id="gluten" v-if="item.gluten_free == 0" style="float: right;"> &nbsp; &nbsp; G </span>
      <span  id="vegan" v-if="item.vegan == 1" style="float: right;"> &nbsp; &nbsp; V </span>
    </label>
    </div>
    <div v-if="item.category == 4">
    <label>
      <label class="ingrtext">
      <button :class="['menu-button', {'focused-category' : item.category === 4}]" type: v-on:click="incrementCounter"> Välj </button>
      {{item["ingredient_"+ lang]}}, {{item.selling_price}}:-
    </label>
      <span id="milk" v-if="item.milk_free == 0" style="float: right;"> &nbsp; &nbsp; L </span>
      <span  id="gluten" v-if="item.gluten_free == 0" style="float: right;"> &nbsp; &nbsp; G </span>
      <span  id="vegan" v-if="item.vegan == 1" style="float: right;"> &nbsp; &nbsp; V </span>
    </label>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Ingredient',
  props: {
    item: Object,
    lang: String,
    currentCategory:Number,
    thecounter:Number
  },
    data: function () {
    return {
      counter: 0
    };
  },
  methods: {
    incrementCounter: function () {
      if (this.currentCategory == 4) {
        this.$emit('deincrement');
        this.$emit('increment');
      }
      else if (this.currentCategory != 4) {
      this.counter += 1;
      // sending 'increment' message to parent component or view so that it
      // can catch it with v-on:increment in the component declaration
      this.$emit('increment');
    }
    },
    incrementDeCounter: function () {

      if(this.thecounter>0){
      // sending 'increment' message to parent component or view so that it
      // can catch it with v-on:increment in the component declaration
      //this.$emit('increment');
      this.$emit('deincrement');
    }

    },
    resetCounter: function () {
      this.counter = 0;
    },
    addtoCounterto:function(){
    this.counter+=1;
    }
  }
}
</script>
<style scoped>
.ingredient {

}

.ingrtext{
  grid-row: 1;
  grid-column: 1;
}

.focused-category {
  background-color: black;
  color: white;
}
.menu-button {
  background-color: white;
  color: black;
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
</style>
