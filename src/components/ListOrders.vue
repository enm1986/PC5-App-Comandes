<template>
  <div class="order-list">
    <h1>Orders List</h1>
    <div class="order" v-for="order in list" v-bind:key="order.id">
      <div>
        <span>Order: #{{ order.id }} - {{ order.date }}</span>
        <!--<button v-on:click="updateOrder(item)">Update</button>-->
        <button v-on:click="deleteOrder(order)">Delete</button>
      </div>
      <div class="item" v-for="item in order.list" v-bind:key="item.id">
        <span>{{item.name}}</span>
        <span>{{item.price}} €</span>
        <span>{{item.quantity}} uds.</span>
      </div>
    </div>
  </div>
</template>

<script>
import { EventBus } from "./event-bus.js";
export default {
  name: "ListOrders",
  data: function() {
    return {
      list: null
    };
  },

  methods: {
    // leer todos los pedidos
    readAll: function() {
      fetch("http://localhost:3000/orders")
        .then(response => response.json())
        .then(json => (this.list = json));
    },
    // leer un pedido
    readOrder: function(order) {
      console.log(order.id);
      fetch("http://localhost:3000/orders/" + order.id)
        .then(response => response.json())
        .then(json => console.log(json));
    },
    // añadir un pedido a la BD
    createOrder: function(order) {
      fetch("http://localhost:3000/orders", {
        method: "POST",
        body: JSON.stringify({
          date: myDateString(),
          list: order
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8"
        }
      })
        .then(response => response.json())
        .then(json => console.log(json))
        .then(() => this.readAll());
    },
    // eliminar un pedido
    deleteOrder: function(order) {
      fetch("http://localhost:3000/orders/" + order.id, {
        method: "DELETE"
      }).then(() => this.readAll());
    },
    updateOrder: function(order) {
      console.log(order.id);
    }
  },
  mounted: function() {
    this.readAll();
  } /*
  updated: function() {
    this.readAll();
  },*/,
  created: function() {
    EventBus.$on("create-order", order => {
      this.createOrder(order);
    });
  }
};

function myDateString() {
  let date = new Date();
  let year = date.getFullYear();
  let month = date.getMonth() + 1;
  let day = ("0" + date.getDate()).slice(-2);
  let hours = ("0" + date.getHours()).slice(-2);
  let min = ("0" + date.getMinutes()).slice(-2);
  return day + "/" + month + "/" + year + " - " + hours + ":" + min;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.order-list {
  flex-basis: 45%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.order {
  display: flex;
  flex-direction: column;
}
.item {
  display: flex;
  justify-content: space-evenly;
}
</style>
