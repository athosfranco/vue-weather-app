/* MARKUP LANGUAGE */
<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 26 ? 'warm' : ''">
    <main>
      <div class="title-wrapper">
        <img src="./assets/vue-logo.png" class="vue-logo" />
        <h2 class="app-title">ClimaVue</h2>
      </div>

      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Digite um local e pressione Enter"
          autofocus
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ nameConverter(weather.weather[0].main) }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

/* LÓGICA */
<script>
export default {
  name: "app",
  data() {
    return {
      api_key: "5cce68bc65063c41dc60c568553baecb",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      forecast: {},
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&lang=pt_br&APPID=${this.api_key}`)
          .then((res) => {
            return res.json();
          })
          .then(this.setResults);
      }
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "Janeiro",
        "Fevereiro",
        "Março",
        "Abril",
        "Maio",
        "Junho",
        "Julho",
        "Agosto",
        "Setembro",
        "Outubro",
        "Novembro",
        "Dezembro",
      ];
      let days = ["Domingo", "Segunda", "Terça", "Quarta", "Quinta", "Sexta", "Sábado"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day}, ${date} de ${month}/${year}`;
    },
    nameConverter(string) {
      if (string === "Rain") return "Chuvoso";
      else if (string === "Clouds") return "Nublado";
      else if (string === "Clear") return "Céu Limpo";
      else if (string === "Snow") return "Nevando";
      else return string;
    },
  },
};
</script>

/*FOLHA DE ESTILO */
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #abe9cd;
  background-image: linear-gradient(315deg, #abe9cd 0%, #3eadcf 74%);
  overflow: hidden;
  transition: 1s;
  font-family: "Montserrat";
}

#app.warm {
  background-color: #fad0c4;
  background-color: #f39f86;
  background-image: linear-gradient(315deg, #f39f86 0%, #f9d976 74%);
}

main {
  min-height: 100vh;
  padding: 25px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 8px 8px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

@media (min-width: 1024px) {
  .search-box .search-bar {
    width: 400px;
    margin: 0 auto;
  }
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 8px 8px 16px rgba(0, 0, 0, 0.25);
}

.location-box {
  margin-top: 30px;
}
.location-box .location {
  color: #fff;
  font-size: 48px;
  font-weight: 500px;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.weather-wrap {
  display: flex;
  flex-direction: column-reverse;
  justify-content: center;
  align-items: center;
  margin-top: 100px;
}

.location-box .date {
  color: #fff;
  font-size: 25px;
  font-weight: 300px;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 110px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

  /* From https://css.glass */
  background: rgba(255, 255, 255, 0.3);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.weather-box .weather {
  color: #fff;
  font-size: 36px;
  font-weight: 700;
  text-shadow: 3px 3px rgba(0, 0, 0, 0.25);
}

.app-title {
  font-size: 30px;
}

.vue-logo {
  width: 50px;
}

.title-wrapper {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}
</style>
