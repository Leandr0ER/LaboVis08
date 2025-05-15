<!-- <h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p> -->
<script>
import mapboxgl from "mapbox-gl";
import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
import { onMount } from "svelte";
import * as d3 from "d3";

mapboxgl.accessToken = "pk.eyJ1IjoibGVpdG9uNjgiLCJhIjoiY21hcGg5azN1MGg4bDJvcHl3Y2V0emtubSJ9.5rroCSCepwuVL_vZ8PtB_A";

let map;
let stations = []

async function loadStationData() {
    try {
        const csvUrl = 'https://vis-society.github.io/labs/8/data/bluebikes-stations.csv';
        const data = await d3.csv(csvUrl);
        
        stations = data.map(station => ({
            id: station.Number,
            name: station.NAME,
            Lat: +station.Lat,
            Long: +station.Long,
        }));
        // console.log('Stations loaded:', stations.length);
    } catch (error) {
        console.error('Error loading station data:', error);
    }
}

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

    // Step 2.4
    	map.addSource("cambridge_route", {
		type: "geojson",
		data: "https://raw.githubusercontent.com/cambridgegis/cambridgegis_data/main/Recreation/Bike_Facilities/RECREATION_BikeFacilities.geojson",
	});


    // Step 2.2
    map.addLayer({
        id: "SOME_ID", // A name for our layer (up to you)
        type: "line", // one of the supported layer types, e.g. line, circle, etc.
        source: "boston_route", // The id we specified in `addSource()`
        paint: {
            // paint params, e.g. colors, thickness, etc.
            'line-color': "green",
            'line-width': 1,
            'line-opacity': 1,
        },
    });

    // Step 2.4 (Cambridge bike lanes)
    map.addLayer({
        id: "cambridge_bike_lanes", // A unique ID for the Cambridge layer
        type: "line",
        source: "cambridge_route", // Use the Cambridge data source
        paint: {
            'line-color': "green", // Let's make them a different color to distinguish them
            'line-width': 1,
            'line-opacity': 1,
        },
    });

    

}

function getCoords (station) {
	let point = new mapboxgl.LngLat(+station.Long, +station.Lat);
	let {x, y} = map.project(point);
	return {cx: x, cy: y};
}

onMount(() => {
	initMap();
    loadStationData();

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
<!-- <div id="map" /> -->

<div id="map">
	<svg></svg>
</div>

<style>
@import url("$lib/global.css");

#map svg {
	/* background: yellow; */
	/* opacity: 50%; */
    position: absolute;
    z-index: 1;
    width: 100%;
    height: 100%;
    pointer-events: none;
}


</style>
