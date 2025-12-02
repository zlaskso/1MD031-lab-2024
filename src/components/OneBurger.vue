<template>
  <div class="box">
    <h3>{{ burger.name }}</h3>
    
    <img :src="burger.url" :alt="burger.name" width="200">
    
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.lactose">
        <span class="allergen">Laktosfri</span>
      </li>
      
      <li v-if="burger.gluten">
        <span class="allergen">Glutenfri</span>
      </li>
      <li v-if="burger.vegetarian">
        <span class="vegetarian">Vegetarisk</span>
      </li>
    </ul>
   <div class="amount">
      <button v-on:click="removeBurger"> - </button>
      <span> {{ amountOrdered }} </span>
      <button v-on:click="addBurger"> + </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', {name: this.burger.name, 
      amount: this.amountOrdered });
    },
    removeBurger: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('orderedBurger', {name: this.burger.name, 
        amount: this.amountOrdered });
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.box {
  padding: 10px;
}
.allergen {
  font-weight: bold;
}
</style>