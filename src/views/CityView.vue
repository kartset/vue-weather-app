<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                Loading....
            </template>
        </Suspense>
    </div>
</template>

<script lang="ts" setup>
import AsyncCityView from '@/components/AsyncCityView.vue';


</script>
