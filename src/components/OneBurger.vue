<template>
  <div>
    <h3> {{ burger.name }} </h3>
    <img :src="burger.img" alt="Span" title="{{ burger.name }}" style="width: 200px">
    <!-- To show the image of the Burger-->
    <ul>
      <li> {{ burger.kCal }} kCal </li>
      <li> <span class="ele">{{ burger.gluten? 'Includes' : 'Gluten'}}</span>-free </li>
      <li> <span class="ele">{{ burger.lactose? 'Includes' : 'Lactose'}}</span>-free </li>
      <li> {{ burger.vegan || burger.vegetarian? burger.vegan? 'Vegan' : 'Vegetarian' : '' }} </li>
    </ul>

    <span>Amount: </span>
    <button v-on:click="DecreaseAmount">-</button>
    <span>{{ amountOrdered }}</span>
    <button v-on:click="IncreaseAmount">+</button>
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
      amountOrdered: 0
    };
  },
  methods: {
    IncreaseAmount: function () {
      this.amountOrdered++;
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      }
      );
    },
    DecreaseAmount: function () {
      if(this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('orderedBurger', {
          name: this.burger.name,
          amount: this.amountOrdered
        }
        );
      }
    }
  } 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

button {
    background-color: #f2f2f2;
    text-align: center;
    font-size: 1em;
    margin: 0.5em;
    width: 1.5em;
}

.ele {
    font-weight: bold;
 }

h3 {
    text-align: center;
}

</style>