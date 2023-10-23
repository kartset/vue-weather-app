<template>
    <main class="container text-white" >
        <div class="pt-4 mb-8 relative" >
            <input 
                type="text" placeholder="Search for a City/State" 
                v-model="searchQuery"
                @input="getSearchResults"
                class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow_[0px_1px_0_0_#004E71" 
            />
            <p v-if="searchError">Sorry, something went wrong. please try again</p>
            <p v-if="!serverError &&  mapboxSearchResults && mapboxSearchResults.length == 0">Sorry, something went wrong. please try again</p>
            <template v-else >
                <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
                    <li @click="previewCity(searchResult)" v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer" >{{searchResult.place_name}}</li>
                </ul>
            </template>
        </div>
        <div class="flex flex-col gap-4" >
            <Suspense>
                <CityList />
                <template #fallback ><CityCardSkeleton /></template>
            </Suspense>
        </div>
    </main>
</template>

<script lang="ts" setup>

import { ref } from 'vue';
import axios from 'axios'
import { useRouter } from 'vue-router';
import CityList from '@/components/CityList.vue';
import CityCardSkeleton from '@/components/CityCardSkeleton.vue';

const mapboxApiToken  = import.meta.env.VITE_MAPBOX_API_TOKEN;

const searchQuery = ref('') 
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(false)
const router = useRouter()


const getSearchResults = () => {
    clearTimeout(queryTimeout.value)
    queryTimeout.value = setTimeout(async () => {
        if(searchQuery.value !== "") {
            try {
                const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxApiToken}&types=place`)
                mapboxSearchResults.value = result.data.features
                console.log({mapboxSearchResults:mapboxSearchResults.value})
            return; 
            } catch (e) {
                searchError.value = true
            }
            
        }
        mapboxSearchResults.value = null;
    }, 300)
}

const previewCity = (searchResult: any) => {
    const [city, state] = searchResult.place_name.split(', ')
    router.push({
        name:'cityView',
        params: {state:state.replaceAll(' ', ''), city},
        query:{
            lat: searchResult.geometry.coordinates[1],
            lng: searchResult.geometry.coordinates[0],
            preview: true
        }
    })
}

</script>

<style lang="scss" scoped>

</style>