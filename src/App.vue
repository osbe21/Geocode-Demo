<script setup>
import { ref } from 'vue';
import Header from './components/Header.vue';
import SearchBar from './components/SearchBar.vue';
import Globe from './components/Globe.vue';

const latitude = ref(0);
const longitude = ref(0);

const apiKey = "65d7c6b3db86b436975163sjiafbb62";

function search(location) {
	const data = fetch(`https://geocode.maps.co/search?q=${location}&api_key=${apiKey}`)
		.then(data => data.json())
		.then(data => data[0])
		.then(data => {
			latitude.value = Number(data.lat);
			longitude.value = Number(data.lon) + 360 * (Math.floor(longitude.value / 360) + 1);
			document.querySelector("#globe-layer").style.bottom = "-20%";
		})
		.catch((error) => console.log(error));
}

</script>

<template>
	<Header id="header">Where do you want to go next?</Header>
	<SearchBar @search="search" id="search-bar" />

	<div id="globe-layer">
		<Globe :latitude="latitude" :longitude="longitude" />
	</div>
</template>

<style scoped>

#header {
	position: absolute;
	width: 100%;
}

#search-bar {
	position: absolute;
	width: 100%;
}

#globe-layer {
	position: absolute;
	z-index: -1;

	width: 100%;
	height: 100%;

	left: 50%;
	transform: translateX(-50%);
	bottom: -70%;

	display: flex;
	justify-content: center;
	align-items: center;

	transition: bottom 2s;
}

</style>
