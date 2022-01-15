/* MARKUP LANGUAGE */
<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 26 ? 'warm' : ''">
    <main>
      <a
        href="https://github.com/athosfranco/vue-weather-app"
        target="_blank"
        class="title-wrapper"
        v-if="typeof weather.main !== 'undefined'"
      >
        <img src="./assets/vue-logo.png" class="vue-logo" />
        <h2 class="app-title">ClimaVue</h2>
      </a>

      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Digite um local e pressione Enter"
          autofocus
          v-model="cidadePesquisada"
          @keypress="fetchWeather"
        />
      </div>

      <h2 style="color: #2a3a4b" v-if="fetching">🔎 Buscando dados...</h2>

      <div class="weather-container" v-if="typeof weather.main != 'undefined'">
        <div class="weather-wrap">
          <div class="location-box">
            <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
            <div class="date">{{ date }}</div>
          </div>

          <div class="weather-box">
            <div class="temp">{{ Math.round(weather.main.temp) }}°</div>
            <div class="weather">{{ nameConverter(weather.weather[0].main) }}</div>
          </div>
        </div>

        <div class="forecast-container">
          <div class="info-row" v-for="(day, index) in forecast" v-bind:key="index">
            <div class="info">
              <span class="info-value">
                {{ forecastDate(index + 1).toLocaleDateString() }}
              </span>
            </div>
            <div class="info info-borders">
              <span class="info-value"> {{ day.main.feels_like.toFixed() }}° </span>
              <span class="info-desc">Sensação Térmica</span>
            </div>
            <div class="info info-borders">
              <span class="info-value"> {{ day.main.temp_min.toFixed() }}° </span>
              <span class="info-desc">Mínima</span>
            </div>
            <div class="info info-borders">
              <span class="info-value"> {{ day.main.temp_max.toFixed() }}° </span>
              <span class="info-desc">Máxima</span>
            </div>

            <span></span>
          </div>
        </div>
      </div>

      <a
        class="title-wrapper-big"
        href="https://github.com/athosfranco/vue-weather-app"
        target="_blank"
        v-if="typeof weather.main === 'undefined'"
      >
        <img src="./assets/vue-logo.png" class="vue-logo-big" />
        <h2 class="app-title-big">ClimaVue</h2>
      </a>
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
      endpoint: "https://api.openweathermap.org/data/2.5/",
      cidadePesquisada: "",
      weather: {},
      forecast: {},
      date: "",
      fetching: false,
    };
  },
  methods: {
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
    forecastDate: (days) => {
      const date = new Date();
      //adiciona a qtd de dias
      date.setDate(date.getDate() + days);
      return date;
    },
    fetchWeather(e) {
      if (e.key == "Enter") {
        this.fetching = true;
        this.date = this.dateBuilder();
        fetch(`${this.endpoint}weather?q=${this.cidadePesquisada}&units=metric&lang=pt_br&APPID=${this.api_key}`)
          .then((res) => {
            return res.json();
          })
          .then(this.setWeatherData);
        fetch(`${this.endpoint}forecast?q=${this.cidadePesquisada}&units=metric&cnt=7&appid=${this.api_key}`)
          .then((res) => {
            return res.json();
          })
          .then(this.setForecastData);
      }
    },
    setWeatherData(results) {
      this.weather = results;
      console.log(results);
      this.fetching = false;
    },
    setForecastData(results) {
      this.forecast = results.list;
      console.log(results.list);
    },
    nameConverter(string) {
      if (string === "Rain") return "Chuvoso";
      else if (string === "Clouds") return "Nublado";
      else if (string === "Clear") return "Céu Limpo";
      else if (string === "Snow") return "Nevando";
      else if (string === "Haze") return "Névoa";
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
  background-color: #eec0c6;
  background-image: linear-gradient(315deg, #eec0c6 0%, #7ee8fa 74%);

  overflow: hidden;
  transition: 1s;
  font-family: "Montserrat";
}

#app.warm {
  background-color: #f9c1b1;
  background-image: linear-gradient(315deg, #f9c1b1 0%, #fb8085 74%);
}

main {
  min-height: 100vh;
  max-width: 100vw;
  padding: 25px;
  overflow: hidden;
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
.app-title-big {
  font-size: 50px;
}

.vue-logo-big {
  width: 80px;
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
  text-decoration: none;
  color: #2a3a4b;
}
.title-wrapper-big {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  position: absolute;
  left: 40%;
  top: 40%;
  text-decoration: none;
  color: #2a3a4b;
  transition: all 0.4s ease;
}
.title-wrapper-big:hover {
  transform: scale(1.2);
}
@media (max-width: 968px) {
  .title-wrapper-big {
    left: 5%;
    top: 40%;
  }
}
.weather-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 500px;
  margin: 0 auto;
  margin-top: 40px;
  background: rgba(255, 255, 255, 0.25);
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
  backdrop-filter: blur(4px);
  -webkit-backdrop-filter: blur(4px);
  border-radius: 10px;
  border: 1px solid rgba(0, 0, 0, 0.18);
}
@media (max-width: 968px) {
  .weather-container {
    width: 100%;
  }
}

.info {
  display: flex;
  flex-direction: column;
  width: 100%;
  align-items: center;
  justify-content: center;
  margin: 10px;
  color: #ffffff;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.info-borders {
  border-right: 1px solid rgba(255, 255, 255, 0.5);
  border-left: 1px solid rgba(255, 255, 255, 0.5);
  font-size: 2rem;
}
@media (max-width: 968px) {
  .info-borders {
    font-size: 1.5rem;
  }
}
.forecast-container {
  display: flex;
  flex-direction: column;
  background-color: none;
  margin-top: 20px;
}

.info-row {
  display: flex;
}
.info-desc {
  font-size: 14px;
}
</style>
