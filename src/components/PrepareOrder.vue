<template>
  <div class="prepare-order">
    <h1>Prepare Order</h1>
    <div
      class="card"
      v-for="item in list"
      v-bind:class="{minus10: item.minus10}"
      v-bind:key="item.id"
    >
      <span>{{ item.name }}</span>
      <div>
        <span>{{ item.price }} € x</span>
        <input v-model="item.quantity" type="number" min="1" max="99" />
        <span>uds. = {{ item.price * item.quantity }} €</span>
      </div>
      <button v-on:click="deleteProduct(item)">Delete</button>
    </div>
    <h2>Total: {{ calcTotal }} €</h2>
    <div class="prepare-menu">
      <label for="product">Product:</label>
      <input type="text" v-model="inName" v-on:keyup.enter="addProduct" />
      <label for="price">Price:</label>
      <input type="text" v-model="inPrice" v-on:keyup.enter="addProduct" size="4" />€
      <button v-on:click="addProduct">Add</button>
      <button v-on:click="deleteLast">Delete last</button>
    </div>
    <br />
    <button v-on:click="addOrder">Add Order</button>
  </div>
</template>

<script>
export default {
  name: "PrepareOrder",
  data: function() {
    return {
      inName: "", //nombre del producto en el input
      inPrice: 0, //precio del producto en el input
      list: [] //lista de productos
    };
  },
  computed: {
    // calcula el precio total de la lista
    calcTotal: function() {
      let total = 0;
      this.list.forEach(function(element) {
        total += element.price * element.quantity;
      });
      return total;
    }
  },
  methods: {
    // añadir un producto a la lista
    addProduct: function() {
      if (Number(this.inPrice) > 0 && this.inName.trim()) {
        this.list.push({
          name: this.inName,
          price: Number(this.inPrice),
          quantity: 1,
          minus10: Number(this.inPrice) < 10
        });
      }
      this.inName = "";
      this.inPrice = 0;
    },
    // eliminar el último elemento de la lista
    deleteLast: function() {
      this.list.pop();
    },
    // eliminar un producto en particular
    deleteProduct: function(product) {
      this.list.splice(this.list.indexOf(product), 1);
    },
    // añade el pedido a la BD
    addOrder: function() {
      fetch("http://localhost:3000/orders", {
        method: "POST",
        body: JSON.stringify({
          date: myDateString(),
          list: this.list
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8"
        }
      })
        .then(response => response.json())
        .then(json => console.log(json));

      this.list = [];
    }
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
.prepare-order {
  display: flex;
  flex-direction: column;
  width: 50%;
}

.prepare-menu {
  display: flex;
  justify-content: flex-start;
}

.card {
  padding: 5px;
  background-color: bisque;
  border: 1px solid black;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.card > span {
  flex-basis: 30%;
}

.card > div {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  flex-basis: 60%;
}

.card > div > * {
  margin: 0 10px;
}

.card > div > input {
  width: 50px;
}

.minus10 {
  color: red;
}
</style>
