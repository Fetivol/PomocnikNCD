<template>
    <div class="weather">
        <form v-on:submit.prevent="getWeather">
            <ion-grid style="background: transparent;">
                <ion-row
                        style="background: transparent; margin-top: 66px;"
                        color="primary"
                        justify-content-center
                >
                    <ion-col
                            style="background: transparent;"
                            align-self-center
                            size-md="6"
                            size-lg="5"
                            size-xs="12"
                    >
                        <div padding style="background: transparent;">
                            <ion-item style="--background: transparent;">
                                <ion-input
                                        class="item-highlight item-inner-highlight"
                                        style="--background: transparent; color: white; font-weight: bolder; font-size: larger;"
                                        @ionChange="getCity"
                                        type="text"
                                        name="city"
                                        placeholder="Wpisz nazwę miasta"
                                ></ion-input>
                            </ion-item>
                        </div>
                        <ion-button
                                style="font-weight: bolder;"
                                v-bind:disabled="isDisabled"
                                type="submit"
                        >Szukaj
                        </ion-button
                        >
                    </ion-col>
                </ion-row>
            </ion-grid>
        </form>
        <ion-grid style="background: transparent;">
            <ion-row
                    style="background: transparent;"
                    color="primary"
                    justify-content-center
            >
                <ion-col
                        style="background: transparent;"
                        align-self-center
                        size-md="6"
                        size-lg="5"
                        size-xs="12"
                >
                    <ion-card
                            style="background: transparent; box-shadow: 0 13px 26px 58px rgba(0,0,0,.2), 0 16px 53px 0 rgba(0,0,0,.14), 0 1px 5px 0 rgba(0,0,0,.12); -webkit-box-shadow:  0 13px 26px 58px rgba(0,0,0,.2), 0 16px 53px 0 rgba(0,0,0,.14), 0 1px 5px 0 rgba(0,0,0,.12);"
                    >
                        <ion-card-header style="background: transparent;">
                            <ion-card-title style="color: white; font-weight: bolder;">{{
                                country && city ? city + ", " + country : "Nie znaleziono"
                                }}
                            </ion-card-title>
                        </ion-card-header>
                        <ion-item
                                style="--background: transparent;"
                                lines="none"
                                text-center
                        >
                            <ion-label
                                    style="--background: transparent; color: white; font-weight: bolder;"
                            >{{ weatherDescription ? weatherDescription : "" }}
                            </ion-label
                            >
                        </ion-item>

                        <ion-item
                                style="--background: transparent;"
                                lines="none"
                                text-center
                        >
                            <ion-label
                                    style="color: white; font-weight: bolder;padding: 10px;"
                                    :style="{'background':airColor}"
                            >{{ airQuality ? airQuality : "" }}
                            </ion-label
                            >
                        </ion-item>

                        <ion-item
                                style="display: -webkit-inline-box !important; margin: 0 auto!important; --background: transparent;"
                                lines="none"
                        >
                            <ion-img :src="icon" v-if="icon" alt="Weather Icon"></ion-img>
                            <ion-label
                                    style="--background: transparent; color: white; font-weight: bolder;"
                            >{{ temp ? temp + "°C" : "" }}
                            </ion-label
                            >
                        </ion-item>
                        <ion-item style="--background: transparent;" line="none">
                            <ion-label
                                    style="--background: transparent; color: white; font-weight: bolder;"
                            >{{ tempMin ? tempMin + "°C" : "" }}
                            </ion-label
                            >
                            <ion-label
                                    style="--background: transparent; color: white; font-weight: bolder;"
                            >{{ tempMax ? tempMax + "°C" : "" }}
                            </ion-label
                            >
                            <ion-label
                                    style="--background: transparent; color: white; font-weight: bolder;white-space: pre-line;"
                            >{{
                                humidity ? "Wilgotność: " + humidity + "%" : ""
                                }}
                            </ion-label
                            >
                        </ion-item>
                    </ion-card>
                </ion-col>
            </ion-row>
        </ion-grid>
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: "Weather",
        data() {
            return {
                city: "",
                country: "",
                icon: "",
                weatherDescription: "",
                temp: "",
                tempMin: "",
                tempMax: "",
                airQuality: "",
                airColor: "transparent",
                humidity: "",
                API_KEY: "ebb14998b4b3590a9180309a877ebe47",
                API_AIR_KEY: "",
                isDisabled: true,
                timeout: 3000,
            };
        },
        methods: {
            getAir(lat, lon) {
                let config = {
                    headers: {
                        Accept: "application/json",
                        apikey: "WZGIIbfVZZ0ypeA0QuF0NQ677Kwauew1",
                        "Accept-Language": "pl",
                    },
                };
                axios
                    .get(
                        "https://airapi.airly.eu/v2/measurements/point?indexType=AIRLY_CAQI&lat=" + lat + "&lng=" + lon,
                        config
                    )
                    .then(response => {
                        console.log(response.data);
                        let data = response.data;
                        this.airQuality = data.current.indexes[0].description;
                        this.airColor = data.current.indexes[0].color;
                    });
            },
            getWeather(event) {
                // this.getAir(event);
                this.city = event.target[0].value;
                if (this.city.trim() !== "") {
                    axios
                        .get(
                            "https://api.openweathermap.org/data/2.5/weather?q=" +
                            this.city +
                            "&mode=json&units=metric&lang=pl&appid=" +
                            this.API_KEY
                        )
                        .then(response => {
                            console.log(response.data);
                            let data = response.data;
                            this.getAir(data.coord.lat, data.coord.lon);
                            this.country = data.sys.country;
                            this.temp = data.main.temp;
                            this.tempMin = data.main.temp_min;
                            this.tempMax = data.main.temp_max;
                            this.humidity = data.main.humidity;
                            this.weatherDescription = data.weather[0].description;
                            this.icon =
                                "https://openweathermap.org/img/w/" +
                                data.weather[0].icon +
                                ".png";
                        }).catch(() => {
                        this.country = "";
                        this.city = "";
                        this.temp = "";
                        this.tempMin = "";
                        this.tempMax = "";
                        this.humidity = "";
                        this.weatherDescription = "";
                        this.icon = "";
                        this.airQuality = "";
                        this.airColor = "";
                    });
                }
            },
            getCity(event) {
                console.log(event);
                event.detail.value.trim() !== ""
                    ? (this.isDisabled = false)
                    : (this.isDisabled = true);
            },
        },
      mounted() {
        console.log('Weather mounted!');
        if (localStorage.getItem('city'))
          this.city = JSON.parse(localStorage.getItem('city'));
        this.getWeather({target: [ {value: this.city} ]});
      },
      watch: {
        city: {
          handler() {
            console.log('City changed!');
            localStorage.setItem('city', JSON.stringify(this.city));
          },
          deep: true,
        },
      },
    };
</script>

<style scoped>
    .weather {
        margin-top: -4%;
    }

    .item-highlight,
    .item-inner-highlight {
        left: 0;
        right: 0;
        bottom: 0;
        position: absolute;
        --background: rebeccapurple;
    }

    .loading-class {
        --spinner-color: rebeccapurple;
    }
</style>
