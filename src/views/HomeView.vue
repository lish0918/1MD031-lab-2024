<template>

  <header>
    <h1> Welcome to BurgerOnline! </h1>            
    <!-- Main title-->
    <img src="../../img/Restaurant.jpg">
  </header>

  <main>
    <section id="Order">
      <h2> Choose your Burger </h2>
      <p> Click on the images to order your favorite Burger! </p>
      <!-- To order Burger-->

      <div>
        <div class="wrapper">
          <Burger v-for="burger in burgers"
                v-bind:burger="burger" 
                v-bind:key="burger.name"
                v-on:orderedBurger="addToOrder ($event)"
                v-bind:src="burger.img"/>
        </div>
      </div>

    </section>

    <section id="Information"> 
      <h2> Customer Information </h2>
      <p> This is where your Burger will be delivered! </p>
      <!-- To provide customers' information-->

      <form id="form">               
        <p>
          <label for="Full name"> Full name: </label><br>
          <input type="text" v-model="fullName" id="Full name" required="required" placeholder="First- and Last name" class="opt">
          {{ fullName }}
        </p>
        <p>
          <label for="E-mail Address"> E-mail: </label><br>
          <input type="email" v-model="emailAddress" id="E-mail Address" required="required" placeholder="E-mail address" class="opt">
          {{ emailAddress }}
        </p>
        <!-- <p>
          <label for="Street"> Street: </label><br>
          <input type="text" v-model="street" id="Street" required="required" placeholder="Street name">
          {{ street }}
        </p>
        <p>
          <label for="House"> House: </label><br>
          <input type="number" v-model="houseNumber" id="House" required="required" placeholder="House number">
          {{ houseNumber }}
        </p> -->

        <p>
          <label for="Payment"> Payment options: </label><br>
          <select v-model="paymentOption" id="Payment" required="required" class="opt">
              <option disabled value="">Please select payment option</option>
              <option value="Visa"> Visa </option>
              <option value="Mastercard"> Mastercard </option>
              <option value="Paypal"> Paypal </option>
              <option value="Swish"> Swish </option>
          </select>
          {{ paymentOption }}
        </p>

        <p>
          <label for="Gender"> Gender: </label><br>
          <input type="radio" id="Gender" value="Female" v-model="gender"> Female <br>
          <input type="radio" id="Gender" value="Male" v-model="gender"> Male <br>
          <input type="radio" id="Gender" value="Non-binary" v-model="gender"> Non-binary <br>
          <input type="radio" id="Gender" value="Undisclosed" v-model="gender"> Undisclosed <br>
          {{ gender }}
        </p>

        <p>Please indicate where you want your Burger to be delivered:</p>
        <div id="mapCon">
          <div id="map" v-on:click="setLocation">
            <div id="dots">
              <div v-bind:style="{ left: clickLocation.x + 'px', top: clickLocation.y + 'px'}">
                T
              </div>
            </div>
          </div>
        </div>
      </form>
    </section>

    <!-- <button form="form" type="submit" v-on:click="addOrder">  -->
    <button type="submit" v-on:click="addOrder"> 
        <img src="../../img/Order.png" style="width: 20px">
        Order now!
    </button>
  </main>

  <hr>
  <!-- To separate the sections-->

  <footer>
    <p> &copy; BurgerOnline 2024 </p>
   <!-- To show the copy right information-->
  </footer>
</template>

<script>
import menu from '../assets/menu.json'
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
// import { ref } from 'vue'

// const gender = ref('Female')

const socket = io("localhost:3000");


// const MenuItem = function(name,kCal,img,gluten,lactose,vegan,vegetarian) {
//   return {
//         name: name,
//         kCal: kCal,
//         img: img,
//         gluten: gluten,
//         lactose: lactose,
//         vegan: vegan,
//         vegetarian: vegetarian
//     };
// }

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurgers: {},
      fullName: '',
      emailAddress: '',
      // street: '',
      // houseNumber: '',
      paymentOption: '',
      gender: '',
      clickLocation: { x: 0,
            y: 0
          },
      orderIdCounter: 1

      // burgers: [
      //       MenuItem("Bacon Cheese Supreme", 350, "../../img/Bacon and Cheese Supreme.jpg", false, false, true, false),
      //       MenuItem("Cheeseburger Delight", 500,, "../../img/Cheeseburger Delight.jpg", true, false, false, true),
      //       MenuItem("Gourmet Burger", 300, "../../img/Gourmet Burger.jpg", true, true, true, false)
      //   ]
    };
  },
  methods: {
    getOrderNumber: function () {
      return this.orderIdCounter++;
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    setLocation: function (event) {   
      var clientX = event.clientX;
      var clientY = event.clientY;

      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.clickLocation.x = clientX - 10 - offset.x;
      this.clickLocation.y = clientY - 10 - offset.y;
    },
    addOrder: function () {
      console.log(this.fullName);
      console.log(this.emailAddress);
      console.log(this.paymentOption);
      console.log(this.gender);
      console.log(this.clickLocation);

      if (this.fullName=='' || this.emailAddress=='' || this.paymentOption=='' || this.clickLocation.x==0 || this.clickLocation.y==0) {
        return;
      } 

      if (this.orderedBurgers.length === 0) {
        return;
      }

      var orderDetails = {
        orderId: this.getOrderNumber(),
        orderItems: this.orderedBurgers,
        fullName: this.fullName,
        emailAddress: this.emailAddress,
        paymentOption: this.paymentOption,
        gender: this.gender,
        clickLocation: this.clickLocation
      };

      socket.emit('addOrder', orderDetails);
      
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

body {
    font-size: 2vw;
    font-family: 'Times New Roman', serif;
}

:root {
    --body-font-size-percentage: 100%;
}

#mapCon {
    position: relative;
    width: auto;
    height: 20em;
    overflow: scroll;
}

#map {
    background: url('../../img/polacks.jpg');
    position: relative;
    width: 1920px;
    height: 1078px;
    cursor: crosshair;
}

#dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 50%;
    width:1.5em;
    height:1.5em;
    font-size: 1em;
    line-height: 1.5em;
    text-align: center;
}

#Order {
    color: white;
    background-color: black;
    margin-left: 4em;
    margin-right: 4em;
    border: 2px dotted white;
 }
 
.ele {
    font-weight: bold;
 }

.wrapper {
    display: grid;
    grid-gap: 0.5em;
    grid-template-columns: 13em 13em 13em;
    margin-left: 0.5em;
}

#Information {
    color: black;
    margin-left: 4em;
    margin-right: 4em;
    border: 2px dotted black;
 }

button:hover {
    background-color: #aca3a3;
}

button {
    background-color: #f2f2f2;
    text-align: center;
    font-size: 1em;
    margin-top: 1em;
    margin-left: 4em;
    margin-right: 4em;
}

p,h2 {
    margin-left: 1%;
}

h1 {
    font-size: 2em;
    text-align: center;
    position: absolute; 
    top: 1.5em;
    bottom: 1.5em;
    left: 5.5em;
    right: 5.5em;
}

header {
    width: auto;
    margin-left: 4em;
    margin-right: 4em;
    height: 10em;
    opacity: 0.9;
    overflow: hidden;
}

.opt {
    width: 25em;
    padding: 0.5em 0.5em;
}

</style>