<template>
  <div id="app" class="container">
    <div  class="custom-control custom-switch" >
      <input  type="checkbox" class="custom-control-input" id="customSwitch1" v-model="oscuro" />
      <label class="custom-control-label" for="customSwitch1" v-bind:class="{texto:oscuro}"
        >Claro/Oscuro</label
      >
    </div>
    <h1 v-bind:class="{texto:oscuro}">
      Pronostico del tiempo
      <img
        alt="Vue logo"
        src="https://img.icons8.com/emoji/96/000000/sun-emoji.png"
      />
    </h1>

    <div class="row" style="margin-left: 80px">
      <div class="col-7">
        <input
          type="text"
          class="form-control form-control-sm"
          id="formGroupExampleInput"
          placeholder="Asuncion"
          style="width: 130%; height: 40px"
          v-model="ciudad"
        />
      </div>
      <div class="col-5" style="margin-top: 0px">
        <button
          class="btn btn-primary"
          style="height: 40px"
          v-on:click="cambiarCiudad()"
        >
          <svg
            width="1em"
            height="1em"
            viewBox="0 0 16 16"
            class="bi bi-search"
            fill="currentColor"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              d="M10.442 10.442a1 1 0 0 1 1.415 0l3.85 3.85a1 1 0 0 1-1.414 1.415l-3.85-3.85a1 1 0 0 1 0-1.415z"
            />
            <path
              fill-rule="evenodd"
              d="M6.5 12a5.5 5.5 0 1 0 0-11 5.5 5.5 0 0 0 0 11zM13 6.5a6.5 6.5 0 1 1-13 0 6.5 6.5 0 0 1 13 0z"
            />
          </svg>
        </button>
      </div>
    </div>

    <div class="d-flex" v-if="cargando != false">
      <div
        class="card p-4 mx-auto cardprueba"
        style="margin-top: 40px; width: 27rem"
      >
        <img
          class="card-img-top mi-imagen"
          src="/background.jpeg"
          alt="Card image "
          style="width: 100%; height: 400px; border-radius: 10px"
        />
        <div class="card-img-overlay">
          <div class="weather-icon">
            <img :src="clima.imgUrl" width="25%" />
          </div>
          <h5 class="card-title">{{ clima.name }}, {{ clima.sys.country }}</h5>
          <h3>{{ clima.dt | fecha }}</h3>
          <h4>{{ clima.weather[0].description | mayuscula }}</h4>

          <hr class="style13" style="width: 90%; margin-left: 20px"  />
          <div class="row">
            <div class="col-4">
              <div class="header">Temperatura</div>
              <div class="value">
                <h3>{{ clima.main.temp | temperatura }}<sup>°</sup>C</h3>
              </div>
            </div>
            <div class="col-4">
              <div class="header">Nubosidad</div>
              <div class="value">
                <h3>{{ clima.clouds.all }}%</h3>
              </div>
            </div>
            <div class="col-4">
              <div class="header">Humedad</div>
              <div class="value">
                <h3>{{ clima.main.humidity }}%</h3>
              </div>
            </div>
          </div>
          <hr class="style13" style="width: 50%; margin-left: 90px" />
          <h3><span> </span>{{ clima.timezone | moment }}</h3>
        </div>
      </div>
    </div>
    <div
      class="spinner-border text-success"
      role="status"
      style="margin-top: 200px"
      v-else
    >
      <span class="sr-only">Loading...</span>
    </div>
  </div>
</template>
 <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
import axios from "axios";
import moment from "moment";

export default {
  name: "App",
  filters: {
    mayuscula: function (value) {
      value = value.toString();
      return value.charAt(0).toUpperCase() + value.slice(1);
    },
    fecha: function (value) {
      return moment(value * 1000).format("LL");
    },
    timestampToDate: function (timestamp) {
      var date = new Date(timestamp * 1000);
      var timestr = date.toLocaleTimeString();
      return timestr;
    },
    temperatura: function (value) {
      return Math.round(value - 273.15);
    },
    moment: function (value) {
      const timezoneInMinutes = value / 60;
      const currTime = moment().utcOffset(timezoneInMinutes).format("h:mm A");
      return currTime;
    },
  },
  mounted() {
    this.getClima();
    //setInterval(this.getClima, 60000);

    // setInterval(function(){ alert("Hello"); }, 3000);
  },
  data() {
    return {
      urlBase: "http://openweathermap.org/img/wn",
      prueba:
        "https://api.openweathermap.org/data/2.5/weather?q=Asuncion&appid=3c51dd953fbbb8456ff4f51128f6212f&lang=es",
      urlapi: "https://api.openweathermap.org/data/2.5/weather?q=",
      id: "&appid=3c51dd953fbbb8456ff4f51128f6212f&lang=es",
      ciudad: null,
      clima: {},
      cargando: false,
      oscuro: false,
    };
  },
  watch:{
      oscuro : function(value){
        this.cambiarModo();
      }
  },
  methods: {
    getClima: function () {
      console.log("hola");
      //this.cargando = true;
      axios
        .get(this.prueba)
        .then((response) => {
          this.clima = response.data;
          // this.clima.temperatura = response.data.temperatura
          this.clima.imgUrl = `${this.urlBase}/${response.data.weather[0].icon}.png`;
        })
        .catch((error) => {
          this.cargando = false;
          window.console.log(error);
          this.cargando = false;
        })
        .finally(() => {
          this.cargando = true
          //console.log("hola");
        });
    },
    cambiarCiudad: function () {
      this.prueba = `${this.urlapi}${this.ciudad}${this.id}`;
      this.getClima();
    },
    cambiarModo: function () {
      var cuerpoweb = document.body;
      cuerpoweb.classList.toggle("oscuro");
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.cardprueba {
  border: 0px;
  border-radius: 25px;
  color: cornsilk;
}
.card {
  background: rgba(0, 0, 0, 0.0001);
}
.mi-imagen {
  -webkit-filter: blur(3px);
  filter: blur(2px);
}
.oscuro {
  background-color: #1f1f1f;
  color: #f1eded;
}
.texto{
   color: cornsilk;
}
</style>
