<template>
    <div class="order-list">
        <h1>Orders List</h1>
        <div class="order" v-for="order in list" v-bind:key="order.id">
            <div class="order-header">
                <span>Order: #{{ order.id }} - {{ order.date }}</span>
                <div>
                    <button v-on:click="prepareUpdate(order.id)">Update</button>
                    <button v-on:click="deleteOrder(order)">Delete</button>
                </div>
            </div>
            <div class="order-item" v-for="item in order.list" v-bind:key="item.id">
                <span>{{item.name}}</span>
                <span>{{item.price}} €</span>
                <span>x {{item.quantity}} =</span>
                <span>{{ item.price * item.quantity }} €</span>
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
            list: null,
            send: null
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
        readOrder: function(id) {
            fetch("http://localhost:3000/orders/" + id)
                .then(response => response.json())
                //.then(json => this.send = json)
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
                //.then(response => response.json())
                //.then(json => console.log(json))
                .then(() => this.readAll());
        },
        // eliminar un pedido
        deleteOrder: function(order) {
            fetch("http://localhost:3000/orders/" + order.id, {
                method: "DELETE"
            }).then(() => this.readAll());
        },
        updateOrder: function(id, list) {
            console.log(id); // MODIFICAR MÉTODO
            console.log(list); // MODIFICAR MÉTODO
        },
        prepareUpdate: function(id) {
            EventBus.$emit("change-update", id);
            //EventBus.$emit("get-list", this.send);
        }
    },
    mounted: function() {
        this.readAll();
    },
    // Escucha de ventos para crear y actualizar
    created: function() {
        EventBus.$on("create-order", order => {
            this.createOrder(order);
        });
        EventBus.$on("update-order", (id, list) => {
            this.updateOrder(id, list);
        });
        EventBus.$on("read-order", id => {
            this.readOrder(id);
            console.log(this.send);
        });
    }
};

function myDateString() {
    let date = new Date();
    let year = date.getFullYear();
    let month = ("0" + (date.getMonth() + 1)).slice(-2);
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
    align-content: flex-start;
    background-color: lightgrey;
    padding: 10px;
}

.order {
    display: flex;
    flex-direction: column;
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