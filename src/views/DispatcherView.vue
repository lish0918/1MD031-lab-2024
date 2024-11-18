<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, index) in orders" :key="index">
          <p>
            #{{ order.orderId }}:
            {{ Object.keys(order.orderItems).map(burgerName => burgerName).join(', ') }}
          </p>
          <p>
            <span style="font-style: italic">{{ order.fullName }} ({{ order.emailAddress }}, {{ order.paymentOption }}, {{ order.gender }})</span>
          </p>
          <hr>
        </div>
      </div>
      <div id="dots">
          <div v-for="(order, index) in orders" :key="index" v-bind:style="{left: order.clickLocation.x + 'px', top: order.clickLocation.y + 'px'}">
            {{ order.orderId }}
          </div>
      </div>
    </div>
  </template>
  
  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: null,
        clickLocation: { x: 0,
            y: 0
          },
        target: 0
      }
    },
    created: function () {
    socket.on('currentQueue', data =>
      this.orders = data.orders);
  },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});
      }
    }
  }
  </script>

  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
    font-size: 0.5em;
  }

  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
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
  </style>
  