<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer" > 
        <div class="flex flex-col flex-1" >
            <h2 class="text-3xl" >{{ props.city.city }}</h2>
            <h3>{{ props.city.state }}</h3>
        </div>

        <div class="flex flex-col gap-2" >
            <p class="text-3xl self-end" >
                {{ Math.round(props.city.weather.data.main.temp) }}&deg;
            </p>
            <div class="flex gap-2" >
                <span class="text-xs" >
                    H:
                    {{ Math.round(props.city.weather.data.main.temp_max)}}&deg;
                </span>
                <span class="text-xs" >
                    L:
                    {{ Math.round(props.city.weather.data.main.temp_min)}}&deg;
                </span>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">

let props = defineProps<{city:any}>()

</script>