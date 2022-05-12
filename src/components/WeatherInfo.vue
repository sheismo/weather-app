<template>
    <div class="container" ref="app">
        <div class="alert" ref="alertMessage">
          <p>Welcome to my weather app!</p>
        </div>
        <div class="my-app">
            <header>
                <h2>&lt;MY WEATHER APP&#47;&gt;</h2>
            </header>
            <div class="content">
                <h1 class="location">
                    <span class="city">{{ city }}</span>,
                    <span class="country">{{ country }}</span>
                </h1>

                <div class="spans">
                    <img src='@/assets/icons/day/cloudy.png' alt="Weather Icon" class="icon" ref="icon">
                    <div>
                        <span class="temperature">
                            {{ temperature }}&#176;
                        </span>
                        <br>
                        <span class="weather-state">{{ weatherState }}</span>
                    </div>
                </div>

                <div class="date-time">
                    <i class="bi bi-calendar-check-fill"></i>
                    <span class="date"> {{ date }}</span>
                    <span class="hyphen"> - </span>
                    <span class="time">{{ time }}</span>
                </div>

                <ul>
                    <p>More Information:</p>
                    <li>
                        Humidity -
                        <span class="humidity"> {{ humidity }}%</span>
                    </li>
                    <li>
                        Cloudy -
                        <span class="cloudy"> {{ cloudy }}%</span>
                    </li>
                    <li>
                        Windy -
                        <span class="windy"> {{ windy }}km/hr</span>
                    </li>
                </ul>

                <div class="form">
                    <input @keypress.enter.prevent="submit" type="search" placeholder="Search for a city..." class="search" ref="search">
                    <button @click="submit" type="submit" class="submit">Search</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default ({
  name: 'WeatherInfo',
  data () {
    return {
      city: 'London',
      country: 'UK',
      temperature: 16,
      weatherState: 'Partly Cloudy',
      date: 'Tuesday April 26 2022',
      time: '2:41pm',
      humidity: '79',
      cloudy: '63',
      windy: '10',
      getCity: 'London',
      apiKey: '7db099c65236433a9f4122528222104'
    }
  },
  methods: {
    getDay (day, month, year) {
      const days = [
        'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'
      ]
      return days[new Date(`${year}, ${month}, ${day}`).getDay()]
    },
    getMonth (day, month, year) {
      const months = [
        'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'
      ]
      return months[new Date(`${year}, ${month}, ${day}`).getMonth()]
    },
    getWeatherData () {
      fetch(`http://api.weatherapi.com/v1/current.json?key=${this.apiKey}&q=${this.getCity}`)
        .then(response => response.json())
        .then(data => {
          this.city = data.location.name
          this.country = data.location.country

          this.temperature = data.current.temp_c
          this.weatherState = data.current.condition.text

          const myDate = data.location.localtime
          const y = parseInt(myDate.substr(0, 4))
          const m = parseInt(myDate.substr(5, 2))
          const d = parseInt(myDate.substr(8, 2))
          const t = myDate.substr(11)

          this.date = `${this.getDay(d, m, y)}, ${this.getMonth(d, m, y)} ${d} ${y}`
          this.time = t

          this.cloudy = data.current.cloud
          this.humidity = data.current.humidity
          this.windy = data.current.wind_kph

          let currentTime = 'day'
          const code = data.current.condition.code

          if (!data.current.is_day) {
            currentTime = 'night'
          }

          if (code === 1000) {
            this.$refs.app.style.backgroundImage = `url(@/assets/images/${currentTime}/clear.jpg)`
            this.$refs.icon.src = `@/assets/icons/${currentTime}/clear.png`
          } else if (
            code === 1003 ||
            code === 1006 ||
            code === 1009 ||
            code === 1030 ||
            code === 1069 ||
            code === 1087 ||
            code === 1135 ||
            code === 1273 ||
            code === 1276 ||
            code === 1279 ||
            code === 1282
          ) {
            this.$refs.app.style.backgroundImage = `url('@/assets/images/${currentTime}/cloudy.jpg')`
            this.$refs.icon.src = `@/assets/icons/${currentTime}/cloudy.png`
          } else if (
            code === 1063 ||
            code === 1069 ||
            code === 1072 ||
            code === 1150 ||
            code === 1153 ||
            code === 1180 ||
            code === 1183 ||
            code === 1186 ||
            code === 1189 ||
            code === 1192 ||
            code === 1195 ||
            code === 1204 ||
            code === 1207 ||
            code === 1240 ||
            code === 1243 ||
            code === 1246 ||
            code === 1249 ||
            code === 1252
          ) {
            this.$refs.app.style.backgroundImage = `url(@/assets/images/${currentTime}/rainy.jpg)`
            this.$refs.icon.src = `@/assets/icons/${currentTime}/rainy.png`
          } else {
            this.$refs.app.style.backgroundImage = `url(@/assets/images/${currentTime}/snowy.jpg)`
            this.$refs.icon.src = `@/assets/icons/${currentTime}/snowy.png`
          }
        })
        .catch(() => {
          this.$alert('Hello Vue Simple Alert.')
          alert('An error occurred, please try again later')
        })
    },
    getMyLocationWeatherData () {
      let latitude
      let longitude

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(position => {
          latitude = position.coords.latitude
          longitude = position.coords.longitude

          fetch(`http://api.weatherapi.com/v1/current.json?key=${this.apiKey}&q=${latitude},${longitude}`)
            .then(response => response.json())
            .then(data => {
              this.city = data.location.name
              this.country = data.location.country

              this.temperature = data.current.temp_c
              this.weatherState = data.current.condition.text

              const myDate = data.location.localtime
              const y = parseInt(myDate.substr(0, 4))
              const m = parseInt(myDate.substr(5, 2))
              const d = parseInt(myDate.substr(8, 2))
              const t = myDate.substr(11)

              this.date = `${this.getDay(d, m, y)}, ${this.getMonth(d, m, y)} ${d} ${y}`
              this.time = t

              this.cloudy = data.current.cloud
              this.humidity = data.current.humidity
              this.windy = data.current.wind_kph

              let currentTime = 'day'
              const code = data.current.condition.code

              if (!data.current.is_day) {
                currentTime = 'night'
              }

              if (code === 1000) {
                this.$refs.app.style.backgroundImage = `url('@/assets/images/${currentTime}/clear.jpg')`
                console.log(this.$refs.app.style)
                this.$refs.icon.src = `@/assets/icons/${currentTime}/clear.png`
              } else if (
                code === 1003 ||
                code === 1006 ||
                code === 1009 ||
                code === 1030 ||
                code === 1069 ||
                code === 1087 ||
                code === 1135 ||
                code === 1273 ||
                code === 1276 ||
                code === 1279 ||
                code === 1282
              ) {
                this.$refs.app.style.backgroundImage = `url('@/assets/images/${currentTime}/cloudy.jpg')`
                console.log(this.$refs.app.style)
                this.$refs.icon.src = `@/assets/icons/${currentTime}/cloudy.png)`
              } else if (
                code === 1063 ||
                code === 1072 ||
                code === 1150 ||
                code === 1153 ||
                code === 1180 ||
                code === 1183 ||
                code === 1186 ||
                code === 1189 ||
                code === 1192 ||
                code === 1195 ||
                code === 1204 ||
                code === 1207 ||
                code === 1240 ||
                code === 1243 ||
                code === 1246 ||
                code === 1249 ||
                code === 1252
              ) {
                this.$refs.app.style.backgroundImage = 'url("@/assets/images/day/cloudy.jpg")'
                this.$refs.icon.src = `@/assets/icons/${currentTime}/rainy.png`
              } else {
                this.$refs.app.style.backgroundImage = `url('@/assets/images/${currentTime}/snowy.jpg')`
                this.$refs.icon.src = `@/assets/icons/${currentTime}/snowy.png`
              }
            })
            .catch(() => {
              alert('An Error occurred, please try again later.')
            })
        })
      }
    },
    submit () {
      if (this.$refs.search.value.length === 0) {
        this.$refs.alertMessage.style.background = 'red'
        this.$refs.alertMessage.innerHTML = '<p>Please enter a city name!</p>'
        this.$refs.alertMessage.style.display = 'flex'
        setTimeout(() => { this.$refs.alertMessage.style.display = 'none' }, 1500)
        clearTimeout()
      } else {
        this.getCity = this.$refs.search.value
        this.getWeatherData()
        this.$refs.search.value = ''
      }
    },
    getMyLocation () {
      this.$refs.alertMessage.style.background = '#F9AE00'
      this.$refs.alertMessage.innerHTML = '<p>Welcome to my weather app!</p>'
      this.$refs.alertMessage.style.display = 'flex'
      setTimeout(() => { this.$refs.alertMessage.style.display = 'none' }, 1500)
      clearTimeout()
      this.getMyLocationWeatherData()
    }
  },
  mounted () {
    this.getMyLocation()
  }
})
</script>

<style scoped>
/* Base styling */
@import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.8.1/font/bootstrap-icons.css");

*{
    box-sizing: border-box;
}

body{
    margin: 0;
    padding: 0;
    font-family: 'Poppins', sans-serif;
}

/* initial design view for desktop and large screen sizes */
div.container{
    width:100%;
    min-height: 100vh;
    background: url('@/assets/images/night/cloudy.jpg') center no-repeat;
    background-size: cover;
    color: #fff;
    position: relative;
    transition: 500ms;
}

div.container::before{
    width: 100%;
    height: 100%;
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.3);
    z-index: 0;
}

div.my-app{
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
}

div.alert{
    width: 100%;
    height: 40px;
    position: sticky;
    top: 0;
    left: 0;
    display: none;
    justify-content: center;
    align-items: center;
    color: #fff;
    background-color:#F9AE00;
    z-index: 10;
}

header{
    width: 100%;
    display: flex;
    justify-content: flex-start;
    align-items:center;
    background: transparent;
}

h2{
    margin-left: 15px;
    color:#ffe6ac;
}

div.content{
    width: 60%;
    margin: auto;
    text-align: center;
}

h1{
    font-size: 3.1rem;
    font-weight: 600;
}

.spans{
    width: 30%;
    margin: -20px auto 25px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.icon{
    width: 90px;
    height: 90px;
    margin-right: 30px;
}

.spans div{
    text-align: left;
}

.temperature{
    font-size: 2.5rem;
    font-weight: 600;
    margin-bottom: -10px;
}

.weather-state{
    font-size: 0.9rem;
}

.bi{
    color:#F9AE00;
    margin-right: 10px;
}

.date{
    margin-right: 30px;
    font-size: 1.05rem;
}

.time{
    margin-left: 30px;
    font-size: 1.05rem;
}

ul{
    list-style: none;
    width: 60%;
    margin: 20px auto;
    padding: 10px;
    background:rgba(255, 255, 255, 0.35);
    border-radius: 10px;
}

ul>p{
    font-weight: 600;
}

li{
    display: inline-block;
    margin-right: 15px;
}

.form{
    width: 60%;
    margin: auto;
    display: flex;
    justify-content: stretch;
    align-items: center;
}

input{
    width: 70%;
    padding: 10px 14px;
    color: #000;
    outline: none;
    border: 1px solid #F9AE00;
    cursor: pointer;
}

button{
    width: 30%;
    padding: 10px 14px;
    color: #fff;
    background:#F9AE00 ;
    outline:none;
    border: 1px solid #F9AE00;
    cursor: pointer;
}

button:hover{
    background: #ffb70f;
}

/* responsive view for tablets and medium screen sizes */
@media (min-width: 769px) and (max-width: 992px){
    div.container{
        max-height: 110vh;
    }

    div.content{
        width: 75%;
        margin-top: 100px;
    }

    h1{
        font-size: 3rem;
        font-weight: 600;
    }

    .spans{
        width: 35%;
    }

    .icon{
        margin-right: 50px;
    }

    .date, .time{
        font-size: 1.5rem;
    }

    ul{
        width: 75%;
        margin: 50px auto;
    }

    li{
        display: block;
        margin-bottom: 15px;
    }

    .form{
        width: 75%;
        justify-content: stretch;
    }
}

/* responsive view for mobile phones and small screen sizes */
@media (max-width: 768px) {
    div.container{
        max-height: 120vh;
    }

    div.content{
        width: 85%;
        margin-top: 100px;
    }

    h1{
        font-size: 2rem;
    }

    .spans{
        width: 50%;
        margin: 50px auto;
    }

    .date, .time{
        font-size: 1.5rem;
        margin-bottom: 15px;
    }

    ul{
        width: 75%;
        margin: 50px auto;
    }

    li{
        display: block;
        margin-bottom: 15px;
    }

    .form{
        width: 75%;
        flex-direction: column;
        justify-content: stretch;
    }

    input{
        width: 100%;
    }

    button{
        width: 100%;
    }
}

/* responsive view for mobile phones and extra small screen sizes */
@media (max-width: 576px){
    div.container{
        max-height: 140vh;
    }

    div.content{
        width: 95%;
        margin-top: 15px;
    }

    header{
        justify-content: center;
    }

    h2{
        margin-left: 0;
    }

    h1{
        font-size: 1.5rem;
    }

    .spans{
        width: 60%;
        margin: 15px auto;
    }

    .temperature{
        font-size: 2rem;
    }

    .weather-state{
        font-size: 0.8rem;
    }

    .date, .time{
        font-size: 1rem;
        margin-bottom: 15px;
    }

    ul{
        width: 75%;
        margin: 30px auto;
    }

    li{
        display: block;
        margin-bottom: 10px;
    }

    .form{
        width: 75%;
        flex-direction: column;
        justify-content: stretch;
    }

    input{
        width: 100%;
    }

    button{
        width: 100%;
    }
}
</style>
