<template>
    <div class="flex flex-col flex-1">
      	<!-- Overview -->
        <div class="flex flex-col py-12 items-center">
            <AnimatePlaceholder class="max-w-[300px] w-full mb-2" />
            <AnimatePlaceholder class="max-w-[300px] w-full mb-12" />
            <AnimatePlaceholder
            	class="max-w-[300px] h-[100px] w-full mb-12"
            />
            <AnimatePlaceholder class="max-w-[300px] w-full mb-8" />
            <AnimatePlaceholder
            	class="max-w-[300px] h-[75px] w-full"
            />
      	</div>
      	<!-- Hourly -->
		<div class="flex flex-col py-12 px-8 items-center">
			<AnimatePlaceholder
				class="max-w-screen-md h-[100px] w-full mb-12 "
			/>
		</div>
		<!-- Weekly -->
		<div class="flex flex-col py-12 px-8 items-center">
			<AnimatePlaceholder
				class="max-w-screen-md h-[100px] w-full mb-12"
			/>
		</div>
    </div>
</template>

<script lang="ts" setup>
import AnimatePlaceholder from './AnimatePlaceholder.vue';
</script>