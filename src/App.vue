<template>
    <div class="page">
        <Slider v-model="x" progress-color="rgb(280,200,70)"
            :thumb-style="{ backgroundColor: 'rgb(250,200,150)', boxShadow: '0 0 10px black' }"></Slider>

        <Slider v-model="y" :height="7" progress-color="rgb(1,200,130)" :thumb-width="5"
            :thumb-style="{ backgroundColor: 'rgb(1,200,230)', borderRadius: '0' }"></Slider>

        <Slider v-model="z" :height="20" progress-color="rgb(200,20,170)" :thumb-width="0"
            :thumb-style="{ backgroundColor: 'rgb(170,10,130)', borderRadius: '' }"></Slider>

    </div>
</template>

<script setup>
import { Slider, VerticalSlider } from '../'
import { ref } from 'vue'

const x = ref(25);
const y = ref(50);
const z = ref(75);
</script>

<style scoped>
.page {
    display: flex;
    margin: auto;
    justify-content: center;
    align-items: center;
    padding: 50px;
    flex-direction: column;
    gap: 30px;
    width: fit-content;
}
</style>