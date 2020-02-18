<template>
    <div class="order-list">
        <h1>Orders List</h1>
        <div class="order" v-for="order in list" v-bind:key="order.id">
            <div class="order-header">
                <span>Order: #{{ order.id }} - {{ order.date }}</span>
                <div>
                    <button v-on:click="prepareUpdate(order)">Update</button>
                    <button v-on:click="deleteOrder(order.id)">Delete</button>
                </div>
            </div>
            <div class="order-item" v-for="item in order.list" v-bind:key="item.id">
                <span>{{ item.name }}</span>
                <span>{{ item.price }} €</span>
                <span>x {{ item.quantity }} =</span>
                <span>{{ item.price * item.quantity }} €</span>
            </div>
            <span>TOTAL: {{ calcTotal(order.list) }} €</span>
        </div>
    </div>
</template>
<script>
import { EventBus } from "./event-bus.js";
export default {
    name: "ListOrders",
    data: function() {
        return {
            list: null,
        };
    },

    methods: {
        // leer todos los pedidos
        readAll: function() {
            fetch("http://localhost:3000/orders")
                .then(response => response.json())
                .then(json => (this.list = json));
        },
        /*// leer un pedido
        readOrder: function(id) {
            fetch("http://localhost:3000/orders/" + id)
                .then(response => response.json())
                .then(json => console.log(json));
        },*/
        // añadir un pedido a la BD
        createOrder: function(list) {
            fetch("http://localhost:3000/orders", {
                    method: "POST",
                    body: JSON.stringify({
                        date: myDateString(),
                        list: list
                    }),
                    headers: {
                        "Content-type": "application/json; charset=UTF-8"
                    }
                })
                .then(() => this.readAll());
        },
        // eliminar un pedido
        deleteOrder: function(id) {
            fetch("http://localhost:3000/orders/" + id, {
                method: "DELETE"
            }).then(() => this.readAll());
        },
        updateOrder: function(id, list) { // MODIFICAR MÉTODO
            fetch("http://localhost:3000/orders/" + id, {
                    method: "PUT",
                    body: JSON.stringify({
                        date: myDateString(),
                        list: list
                    }),
                    headers: {
                        "Content-type": "application/json; charset=UTF-8"
                    }
                })
                .then(() => this.readAll());
        },
        prepareUpdate: function(order) {
            EventBus.$emit("change-update", order.id, unbindSource(order.list));
        },
        calcTotal: function(list) {
            let total = 0;
            list.forEach(function(element) {
                total += element.price * element.quantity;
            });
            return total;
        }
    },
    mounted: function() {
        this.readAll();
    },
    // Escucha de ventos para crear y actualizar
    created: function() {
        EventBus.$on("create-order", (list) => {
            this.createOrder(list);
        });
        EventBus.$on("update-order", (id, list) => {
            this.updateOrder(id, list);
        });
    }
};

// Devuelvela fecha actual formateada
function myDateString() {
    let date = new Date();
    let year = date.getFullYear();
    let month = ("0" + (date.getMonth() + 1)).slice(-2);
    let day = ("0" + date.getDate()).slice(-2);
    let hours = ("0" + date.getHours()).slice(-2);
    let min = ("0" + date.getMinutes()).slice(-2);
    return day + "/" + month + "/" + year + " - " + hours + ":" + min;
}

// Devuelve una copia no reactiva del array pasado
function unbindSource(source) {
    return JSON.parse(JSON.stringify(source));
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.order-list {
    flex-basis: 45%;
    display: flex;
    flex-direction: column;
    align-content: flex-start;
    background-color: lightgrey;
    padding: 10px;
}

.order {
    display: flex;
    flex-direction: column;
    margin: 5px;
}

.order-header {
    display: flex;
    justify-content: space-between;
    background-color: grey;
    font-weight: bolder;
    padding: 5px 10px;
}

.order-header button {
    margin: 0 5px;
}

.order-item {
    display: flex;
    justify-content: space-evenly;
}
</style>