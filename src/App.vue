<template>
  <div id="app">
    <h1>La Bilouterie Météo</h1>
    <h3>La météo fiable, sure et gratuite</h3>
    <div class="searchBar">
      <div class="searchBar__icon">
        <fa icon="search" class="searchBar__icon--icon" />
      </div>
      <input v-model="cityName" type="text" class="searchBar--input" @keyup.enter="onSearch"/>
    </div>
    <span v-if="errorMsg" class="errorMsg">{{ errorMsg }}</span>
    <span class="searchBtn" @click="onSearch">Rechercher</span>
    <div class="cityPreview">
      <template v-if="!loading">
        <template v-if="temp !== ''">
          <div v-if="imgCode" id="icon">
            <img id="wicon" :src="previewImgLink" alt="Weather icon">
          </div>
          <p class="cityName">
            Nom: <span class="bold">{{ cityName }}</span>
          </p>
          <p class="temperature">
            Température: <span class="bold">{{ temp }}</span> degrés
          </p>
          <p class="wind">
            Vent: <span class="bold">{{ wind }}</span> km/h
          </p>
          <p class="globalWeather">
            Global: <span class="bold">{{ global }}</span>
          </p>
        </template>
        <template v-else>
          <p>Aucune donnée.</p>
        </template>
      </template>
      <template v-else>
        <p>Chargement...</p>
      </template>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      cityName: "",
      temp: "",
      wind: "",
      global: "",
      errorMsg: null,
      imgCode: null,
      loading: false,
    };
  },
  computed: {
    previewImgLink() {
      return `http://openweathermap.org/img/w/${this.imgCode}.png`;
    },
  },
  methods: {
    onSearch() {
      this.errorMsg = null;
      const cityName = this.cityName;
      if (cityName && cityName.length > 3) {
        this.loading = true;
        this.fetchData(cityName)
          .then((response) => {
            const data = response.data;
            const wind = data.wind.speed;
            const temp = Math.floor(data.main.temp);
            const global = data.weather[0].description;
            const imgCode = data.weather[0].icon;

            this.temp = temp;
            this.wind = wind;
            this.global = global;
            this.imgCode = imgCode;
            this.loading = false;
          })
          .catch((error) => {
            console.error(error);
            this.errorMsg = error;
            this.loading = false;
          });
      } else {
        this.errorMsg = 'Nom de ville trop court';
      }
    },
    fetchData(cityName) {
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=8696c85644b85f2592ae0aa764e544d0&lang=fr&units=metric`;
      return new Promise((fnResolve, fnReject) => {
        axios
          .get(url)
          .then(function (response) {
            fnResolve(response);
          })
          .catch(function (error) {
            fnReject(error);
          });
      });
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.cityPreview {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  width: 500px;
  height: 500px;
  background: rgba(180, 215, 255, 0.322);
  border-radius: 5px;
  margin: auto;
  margin-top: 50px;
}

.searchBar {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 300px;
  height: 50px;
  // background: red;
  margin: auto;
  overflow: hidden;
  border-radius: 5px;
  border: 1px solid rgb(180, 215, 255);

  &__icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50px;
    height: 50px;
    background: rgb(180, 215, 255);

    &--icon {
      color: white;
    }
  }

  &--input {
    width: 250px;
    height: 100%;
    border: unset;
    padding-left: 10px;

    &:focus {
      outline: none;
    }
  }
}

.bold {
  font-weight: bold;
}

.searchBtn {
  display: block;
  font-weight: bold;
  margin-top: 10px;
  transition: all 0.25s ease;

  &:hover {
    cursor: pointer;
    opacity: 0.7;
  }
}

.errorMsg {
  color: red;
  font-size: 12px;
}

.wicon {
  height: 60px;
}
</style>
