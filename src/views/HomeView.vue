<template>
  <div> <header>
       <h1>Välkommen till Le Burger Zantoné</h1>
    </header>

    <section id="burgare">
        <h3>Välj burgare</h3>
        <p>Välj någon av våra bästsäljande burgare nedan!</p>

        <div class="burger-grid">
            <Burger 
            v-for="burger in burgers" 
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder"
            />
        </div>
    </section>

    <section id="info">
       <h2>Dina uppgifter</h2>
        <p>Skriv din information nedan</p>
        <h3>Leveransinformation</h3>
        <p>
            <label for="fullname">Namn</label><br>
            <input type="text" id="fullname" v-model="fullname" required="required" placeholder="För- och efternamn">
        </p>
        <p>
            <label for="email">E-mail</label><br>
            <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
        </p>

        <h2>Betalningsinformation</h2>
        <p>
            <label for="Betalningsmetod">Betalningsmetod</label><br>
                <select id="Betalningsmetod" v-model="paymentMethod">
                    <option>Kreditkort</option>
                    <option>Debitkort</option>
                    <option>Klarna</option>
                    <option>Betala i butik</option>
                    <option>American Express</option>
                </select>
        </p>

        <p>Kön</p>

        <label>
            <input type="radio" value="male" v-model="gender">
            Man
        </label>

        <label>
            <input type="radio" value="female" v-model="gender">
            Kvinna
        </label>

        <label>
            <input type="radio" value="Icke-binär" v-model="gender">
            Icke-binär
        </label>

        <label>
            <input type="radio" value="Vill ej ange" v-model="gender">
            Vill ej ange
        </label>
        <h3>Välj leveransplats</h3>
        <p>Klicka på kartan för att välja platsen vi ska leverera till</p>
        <div id="map-wrapper">
        <div id="map" v-on:click="setLocation">
          <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
            T
          </div>
        </div>
      </div>
    </section>

    <button id="submitOrder" v-on:click="printOrderDetails">
        <img src="/img/Submit.png" alt="Beställ">
        Beställ!
    </button>

    <hr>
    <footer>
        <p>Copyright &copy; 2025 Le Burger Zantoné</p>
    </footer>

  </div> </template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, url,  kCal, gluten, lactose, vegetarian) {
  this.name = name;
  this.kCal = kCal;
  this.url = url;
  this.gluten = gluten;
  this.lactose = lactose;
  this.vegetarian = vegetarian;
}

const menuItems = menu.map(item => new MenuItem(
  item.name,
  item.url,
  item.kCal,
  item.gluten,
  item.lactose,
  item.vegetarian
));

console.log(menuItems);


export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      fullname: "",
      email: "",
      paymentMethod: "Kreditkort",
      gender: "",
      orderedBurgers: {},
      location: { x: 0, y: 0  }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },

    printOrderDetails: function () {
      socket.emit("addOrder", { 
        orderId: this.getOrderNumber(),
        details: { 
          x: this.location.x, 
          y: this.location.y 
        },
        orderItems: this.orderedBurgers,
        customerInfo: {
          name: this.fullname,
          email: this.email,
          payment: this.paymentMethod,
          gender: this.gender
        }
      });
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location = { 
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y 
      };
    }
  }
}
</script>

<style>
  #map-wrapper {
  width: 100%;
  height: 400px;
  overflow: scroll;
}

#map {
  width: 1920px;
  height: 1078px;
  background: url("/img/polacks.jpg");
  position: relative;
}

#map div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}

body {
    font-family: "Times New Roman", serif;
    font-size: 12pt;

}

.gluten {
    font-weight: bold;
}
.lactose {
    font-weight: bold;
}


.vegetarian {
    font-weight: bold;
    color: green;
}


#burgare {
    padding: 10px;
    margin: 20px;
    background-color: black;
    color: white;
    border-style: dashed;
    border-width: 2px;
    border-color: white;
}

.burger-grid {
    display: grid;
    grid-template-columns: repeat(3,1fr);
}


#info {
    padding: 10px;
    margin: 20px;
    border-style: dashed;
    border-width: 2px;
    border-color: black;
}

button:hover {
    background-color: yellow;
    cursor: pointer;
}


section {
    margin: 40px;
    padding: 20px;
}

    
header h1  {
z-index: 2;
color: yellow;
font-size: 48pt;
text-shadow: 2px 2px 4px #000000;

}


header {
    width: 100%;
    background-image: url("/img/header.jpg");
    background-size: cover;
    background-position: 50% 70%;
    height: 250px;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    opacity: 0.9;
  
}


</style>