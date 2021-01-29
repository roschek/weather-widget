<template>
  <div class='container'>
    <h1>Виджет погоды</h1>
    <img class='widget__gear' src='../assets/images/gear.svg' alt='gear' v-on:click='changeCityHandler'>
    <div v-show='searchCity'>
      <input class='widget__input' type='text' placeholder='введите название города' v-model='city'>
      <button class='widget__button' v-on:click='seekWeather(city,API_KEY,baseURL)'>Узнать погоду</button>
    </div>
    <div class='card'>
      <p>погода в городе {{ seekCity }}</p>
      <p>температура {{ temperature }}&#176; С</p>
      <p>ощущается как {{ tempFeel }} С</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'


export default {
  data() {
    return {
      city: '',
      seekCity: '',
      temperature: '',
      tempFeel: '',
      API_KEY: '2f7c3b91feb3833e8d77dd60937de463',
      baseURL: 'api.openweathermap.org/data/2.5/',
      searchCity: false,
      componentKey: 0
    }
  },
  components: {},
  methods: {
    seekWeather(city, key, url) {
      axios.get(`https://${url}weather?q=${city}&units=metric&lang=ru&appid=${key}`)
        .then(res => res.data)
        .then(data => {
          this.setData(data)
          this.city = ''
        })
        .catch(err => {
          console.log(err)
          this.seekCity = 'Неизвестный город, обновите страницу'
        })

    },
    setData(res) {
      const response = JSON.stringify(res)
      localStorage.setItem(`city`, response)
      this.seekCity = res.name
      this.temperature = res.main.temp
      this.tempFeel = res.main.feels_like
    },
    getData() {
      let city = JSON.parse(localStorage.getItem('city'))
      this.seekCity = city.name
      this.temperature = city.main.temp
      this.tempFeel = city.main.feels_like
    },
    changeCityHandler() {
      this.searchCity = !this.searchCity
    }

  },
  mounted() {
    if (localStorage.city) {
      this.getData()

    }
  }
}
</script>

<style>
.container {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  box-sizing: border-box;
  max-width: 400px;
  border: 1px solid gray;
  border-radius: 20px;
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 30px;
}

.widget__input {
  padding: 10px;
  color: gray;
  border: none;
  border-bottom: 1px solid gray;
  transition: .3s;
  outline: none;
}

.widget__input:hover {
  border-bottom: 1px solid green;
}

.widget__button {
  padding: 10px;
  border: none;
  border-radius: 20px;
  background: linear-gradient(90deg, #aea4e3, #d3ffe8);
  cursor: pointer;
  transition: .3s;
}

.widget__button:hover {
  box-shadow: 4px 4px 4px 0px rgba(26, 20, 50, 0.34);
}

.widget__gear {
  cursor: pointer;
  width: 20px;
  height: 20px;
  transition: .3s;
}

.widget__gear:hover {
  transform: scale(1.1);
}
</style>
