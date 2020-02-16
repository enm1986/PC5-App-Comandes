<template>
  <div class="order-list">
    <h1>Orders List</h1>
    <div class="order" v-for="order in list" v-bind:key="order.id">
      <span>Order: #{{ order.id }} - {{ order.date }}</span>
      <div class="item" v-for="item in order.list" v-bind:key="item.id">
        <span>{{item.name}}</span>
        <span>{{item.price}} €</span>
        <span>{{item.quantity}} uds.</span>
      </div>
      <!--<button v-on:click="updateOrder(item)">Update</button>-->
      <!--<button v-on:click="deleteOrder(item)">Delete</button>-->
    </div>
  </div>
</template>

<script>
export default {
  name: "ListOrders",
  data: function() {
    return {
      list: null
    };
  },

  methods: {
    fetchData: function() {
      fetch("http://localhost:3000/orders")
        .then(response => response.json())
        .then(json => (this.list = json));
    }
  },

  mounted() {
    this.fetchData();
  }
};
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
