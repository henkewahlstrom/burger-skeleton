
 <template>
  <div class="ingredient">
    <label>
      <button v-on:click="incrementDeCounter"> - </button>
      {{counter}}
      <button v-on:click="incrementCounter"> + </button>
      {{item["ingredient_"+ lang]}}, {{item.selling_price}}:-
      <span  id="milk" v-if="item.milk_free == 0"> L </span>
      <span  id="gluten" v-if="item.gluten_free == 0"> G </span>
      <span  id="vegan" v-if="item.vegan == 1"> V </span>
    </label>
  </div>
</template>
<script>
export default {
  name: 'Ingredient',
  props: {
    item: Object,
    lang: String
  },
    data: function () {
    return {
      counter: 0
    };
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1;
      // sending 'increment' message to parent component or view so that it
      // can catch it with v-on:increment in the component declaration
      this.$emit('increment');
    },
    incrementDeCounter: function () {
      if (this.counter >0) {
      this.counter -= 1;
      // sending 'increment' message to parent component or view so that it
      // can catch it with v-on:increment in the component declaration
      //this.$emit('increment');
      this.$emit('deincrement');
    }
    },
    resetCounter: function () {
      this.counter = 0;
    }
  }
}
</script>
<style scoped>

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
