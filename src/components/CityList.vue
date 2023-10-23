<template>
    
    <div v-for="(city) in savedCities" :key="city.id" >
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>

    <p v-if="savedCities.length === 0" >
        No Locations added. To start tracking a location, search in the field above.
    </p>
</template>

<script lang="ts" setup>

import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';


const savedCities:any = ref([])
const router = useRouter()

const getCities = async () => {

    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities') as string)
    }

    const requests: any[] = []

    savedCities.value.forEach((city:any) => {
        requests.push(
            axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${import.meta.env.VITE_OPENWEATHER_API_TOKEN}&units=imperial`)
        )
    })

    const weatherData = await Promise.all(requests)

    weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value
    })
}

const goToCityView = (city:any) => {
    router.push({
        name:'cityView',
        params: {state:city.state, city:city.city},
        query: {id:city.id, lat:city.coords.lat, lng:city.coords.lng}
    })
}

await getCities()

</script>