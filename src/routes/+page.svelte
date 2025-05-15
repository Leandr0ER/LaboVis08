<!-- <h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p> -->
<script>
import mapboxgl from "mapbox-gl";
import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
import { onMount } from "svelte";

mapboxgl.accessToken = "pk.eyJ1IjoibGVpdG9uNjgiLCJhIjoiY21hcGg5azN1MGg4bDJvcHl3Y2V0emtubSJ9.5rroCSCepwuVL_vZ8PtB_A";

let map;

async function initMap() {
	map = new mapboxgl.Map({
		container: 'map',
		center: [-71.09415, 42.36027],
		zoom: 12,
		style: "mapbox://styles/mapbox/streets-v12",
	});
	await new Promise(resolve => map.on("load", resolve));
	map.addSource("boston_route", {
		type: "geojson",
		data: "https://bostonopendata-boston.opendata.arcgis.com/datasets/boston::existing-bike-network-2022.geojson?outSR=%7B%22latestWkid%22%3A3857%2C%22wkid%22%3A102100%7D",
	});

    // Step 2.2
    map.addLayer({
        id: "SOME_ID", // A name for our layer (up to you)
        type: "line", // one of the supported layer types, e.g. line, circle, etc.
        source: "boston_route", // The id we specified in `addSource()`
        paint: {
            // paint params, e.g. colors, thickness, etc.
        },
    });

}

onMount(() => {
	initMap();
});



// onMount(() => {
// 	let map = new mapboxgl.Map({
// 		container: 'map',
// 		style: 'mapbox://styles/mapbox/streets-v12',
// 		zoom: 12,
// 		center: [-71.0788727679991, 42.36199480654666]
// 	});
// })

</script>

<!-- -------------- -->
<h1>Bikes</h1>
<div id="map" />

<style>
@import url("$lib/global.css");
</style>