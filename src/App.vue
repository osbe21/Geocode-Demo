<script setup>
import { ref } from 'vue';
import Header from './components/Header.vue';
import SearchBar from './components/SearchBar.vue';
import Globe from './components/Globe.vue';

const latitude = ref(0);
const longitude = ref(0);

const headerTop = ref('5%');
const searchBarTop = ref('35%');
const globeBottom = ref('-70%');

const apiKey = "65d7c6b3db86b436975163sjiafbb62";

function search(location) {
	const data = fetch(`https://geocode.maps.co/search?q=${location}&api_key=${apiKey}`)
		.then(data => data.json())
		.then(data => {
			if (data.length) {
				headerTop.value = "-100%";
				searchBarTop.value = "5%";
				globeBottom.value = "-20%";
				
				latitude.value = Number(data[0].lat);
				longitude.value = Number(data[0].lon) + 360 * (Math.floor(longitude.value / 360) + 1);
			} else {
				alert("Location not found");
			}
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
	left: 50%;
	transform: translateX(-50%);
	top: v-bind('headerTop');

	transition: top 2s;
}

#search-bar {
	position: absolute;
	left: 50%;
	transform: translateX(-50%);
	top: v-bind('searchBarTop');
	
	transition: top 2s;
}

#globe-layer {
	position: absolute;
	z-index: -1;

	width: 100%;
	height: 100%;

	left: 50%;
	transform: translateX(-50%);
	bottom: v-bind('globeBottom');

	display: flex;
	justify-content: center;
	align-items: center;

	transition: bottom 2s;
}

</style>
