<script setup>
import { ref } from 'vue';

const emits = defineEmits(['search']);

const location = ref('');
const placeholder = ref('');

let locationIdx = 0;
const placeholderLocations = ['Paris', 'Tokyo', 'London', 'Oslo', 'Seoul', 'Barcelona', 'Washington, D.C.'];
const typingDelay = 100;

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function typeWord(word) {
    while (word) {
        placeholder.value += word[0];
        word = word.slice(1);
        await sleep(typingDelay);
    }

    await sleep(1000);

    while (placeholder.value) {
        placeholder.value = placeholder.value.slice(0, placeholder.value.length-1);
        await sleep(typingDelay);
    }
}

function autoTypeLocations() {
    sleep(2000).then(async () => {
        await typeWord(placeholderLocations[locationIdx % placeholderLocations.length]);
        locationIdx++;
        autoTypeLocations();
    })
};

autoTypeLocations();

</script>


<template>
    <span>
        <form class="container">
            <input v-model="location" type="text" class="source-serif" id="search" autocomplete="off" spellcheck="false" :placeholder="placeholder">
            <button @click.prevent="$emit('search', location)" type="submit" id="go-btn"><img src="/plane.png" alt="Go"></button>
        </form>
    </span>
</template>

<style scoped>

.container {
    display: flex;
    align-items: stretch;
    gap: 30px;

    width: min-content;
    height: 50px;
}

#search {
    display: inline-block;
    width: 300px;

    padding-left: 20px;

    background-color: transparent;

    outline: none;
    border: none;
    border-bottom: 1px white solid;

    color: white;
    font-size: 40px;

    backdrop-filter: brightness(60%) blur(4px);
}

#go-btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 80px;

    background-color: transparent;

    border-radius: 6px;
    border: none;
    border: 1px white solid;

    cursor: pointer;

    transition: all .2s;
}

#go-btn:hover {
    background-color: white;

    box-shadow: 0 0 20px rgba(255, 255, 255, 0.4);
}

#go-btn img {
    display: inline-block;
    width: 50px;
}

#go-btn:hover img {
    filter: invert(1);
}

</style>