<template>
	<div ref="map-root" style="width:100%; height:100%">
	</div>
	
</template>
<script>
import 'ol/ol.css';
import Map from 'ol/Map';
import View from 'ol/View';
import Feature from 'ol/Feature'
import Point from "ol/geom/Point"
import {OSM, Vector as VectorSource} from 'ol/source';
import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer';
import {fromLonLat, transform} from 'ol/proj';
import axios from "axios"


import "ol/ol.css"
let source = new VectorSource();
let vectorLayer = new VectorLayer({
  source: source,
});

let rasterLayer = new TileLayer({
  source: new OSM(),
});

export default {
	name:"MapComtainer", 
	components: {},
   	mounted() {
		navigator.geolocation.getCurrentPosition(
			geolocationCallback,
			geolocationCallbackFail
		);
		async function geolocationCallbackFail() {
			console.log('failed-to-get-locaion');
			new Map({
				target: this.$refs['map-root'],
				layers: [rasterLayer, vectorLayer],
				view: new View({
					zoom: 0,
					center: fromLonLat([0, 0]),
					constrainResolution: true
				})
			});
		}
		async function geolocationCallback(position) {
			//this cant be written as a const so dont change it
			new Map({
				target: this.$refs['map-root'],
				layers: [rasterLayer, vectorLayer],
				view: new View({
					zoom: 15,
					center: fromLonLat([
						position.coords.longitude,
						position.coords.latitude
					]),
					constrainResolution: true
				})
			});
			let pointFeature = new Feature(
				new Point(
					transform(
						[position.coords.longitude, position.coords.latitude],
						'EPSG:4326',
						'EPSG:3857'
					)
				)
			);
			source.addFeature(pointFeature);
		}

		const returnStoreLocations = async (storeName, position) => {
			let reverseGeo = await axios.get(
				`https://api.bigdatacloud.net/data/reverse-geocode-client\?latitude\=${position.coords.latitude}\&longitude\=${position.coords.longitude}\&localityLanguage\=en`
			);
			let storeLocations = await axios.get(`https://nominatim.openstreetmap.org/search?q=`)
		};
	}
}
</script>