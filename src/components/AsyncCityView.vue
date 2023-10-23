<template>

    <div class="flex flex-col flex-1 items-center" >
        <!-- Banner -->
        <div 
            v-if="route.query.preview" 
            class="text-white p-4 bg-weather-secondary w-full text-center"
        >
            <p>
                You are currently previewing this city, click "+"
                icon to start tracking this city
            </p>
        </div>
        <!-- Weather Overview -->
        <div class="flex flex-col items-center text-white py-12" >
            <h1 class="text-4xl mb-2" >{{route.params.city}}</h1>
            <p class="text-sm mb-12" >
                {{ 
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "en-uS",
                        {
                            weekday: "short",
                            day: '2-digit',
                            month: "long"
                        }
                    )
                }} 
                {{ 
                    new Date(weatherData.currentTime).toLocaleTimeString(
                        "en-us",
                        {
                            timeStyle: "short"
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-8" >
                {{ Math.round(weatherData.current.temp)}}&deg;
            </p>
            <p class="text-center" >
                Feels Like
                {{Math.round(weatherData.current.feels_like)}}&deg;
            </p>
            <p class="capitalize" >
                {{ weatherData.current.weather[0].description }}
            </p>
            <img :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" />

            <hr class="border-white border-opacity-10 border w-full" />

            <div class="max-w-screen-md w-full py-12" >
                <div class="mx-8 text-white" >
                    <h2 class="mb-4" >Hourly Weather</h2>
                    <div class="flex gap-10 overflow-x-scroll" >
                        <div v-for="(hourData,i) in weatherData.hourly" :key="i" class="flex flex-col gap-4 items-center" >
                            <p class="whitespace-nowrap text-md" >
                                {{ 
                                    new Date(hourData.currentTime)
                                    .toLocaleTimeString("en-US", {hour:'numeric'})
                                }}
                            </p>
                            <img 
                                class="w-auto h-[50px] object-cover" 
                                :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" 
                                alt=""
                            />
                            <p class="text-xl">
                                {{ Math.round(hourData.temp)}}&deg;
                            </p>
                        </div>
                    </div>
                </div>
            </div>

            <hr class="border-white border-opacity-10 border w-full" />

            <div class="max-w-screen-md w-full py-12" >
                <div class="mx-8 text-white">
                    <h2 class="mb-7" >7 Day Forcast</h2>
                    <div v-for="(day, i) in weatherData.daily" :key="i" class="flex items-center" >
                        <p  class="flex-1" >
                            {{ 
                                new Date(day.dt*1000)
                                .toLocaleDateString("en-US", {weekday:"long"})
                             }}
                        </p>
                        <img 
                            class="w-[50px]h-[50px] object-cover" 
                            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" 
                            alt="" 
                        /> 
                        <div class="flex gap-2 flex-1 justify-end">
                            <p>H: {{ Math.round(day.temp.max) }} </p>
                            <p>l: {{ Math.round(day.temp.min) }} </p>
                        </div>
                    </div>
                </div>
            </div>

            <div @click="removeCity()" class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" >
                <i class="fa-solid fa-trash"></i>
                <p>Remove City</p>
            </div>


        </div>
    </div>
</template>

<script lang="ts" setup>
    import axios from "axios"
    import { useRoute, useRouter } from "vue-router";

    const route = useRoute()
    const router = useRouter()
    
    const getWeatherData = async () => {
        try {
            const weatherData = await axios.get(
                `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&appid=${import.meta.env.VITE_OPENWEATHER_API_TOKEN}&units=imperial`
            )

             // cal current date & time
            const localOffset = new Date().getTimezoneOffset() * 60000;
            const utc = weatherData.data.current.dt * 1000 + localOffset;
            weatherData.data.currentTime =
            utc + 1000 * weatherData.data.timezone_offset;

            // cal hourly weather offset
            weatherData.data.hourly.forEach((hour: { dt: number; currentTime: number; }) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime =
                utc + 1000 * weatherData.data.timezone_offset;
            });

            console.log({weatherData})


            return weatherData.data
        } catch (err) {
            console.log(err)
        } 
    }
    const removeCity = () => {
        const cities = JSON.parse(localStorage.getItem("savedCities") as string)
        const updatedCities = cities.filter((city:any) => city.id != route.query.id)

        localStorage.setItem('savedCities', JSON.stringify(updatedCities))

        router.push({
            name:'home'
        })
    }

    const weatherData = await getWeatherData()
</script>