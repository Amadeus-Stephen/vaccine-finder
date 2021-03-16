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
		   	const getLocationData = async() => {
				let reverse_geo = axios.get(`https://api.bigdatacloud.net/data/reverse-geocode-client\?latitude\=${data.coords.latitude}\&longitude\=${data.coords.longitude}\&localityLanguage\=en`)

			}
			const returnStoreLocations = async(storeName) => {

			}
			const runApp = async() => {

				navigator.geolocation.getCurrentPosition((data)=>{
					let map = new Map({
		   				target: this.$refs["map-root"],
		  	 			layers: [
			 	  			rasterLayer , vectorLayer
		  	 			],
		  	 			view: new View({
			 	  			zoom:15,
			 	  			center: fromLonLat([data.coords.longitude, data.coords.latitude]),
			 	 			constrainResolution:true
		  	 			}),
				
	   				})
					let pointFeature = new Feature(
						new Point(transform([data.coords.longitude ,data.coords.latitude ] , 'EPSG:4326' , 'EPSG:3857'))
					);
					source.addFeature( pointFeature );
					console.log(reverse_geo.data)
				// let stores = axios.get(`https://nominatim.openstreetmap.org/search?q=`)
				}, 
				() => {
					new Map({
			   			target: this.$refs["map-root"],
			   			layers: [
				   			rasterLayer , vectorLayer
			   			],
			   			view: new View({
				   			zoom:0,
				   			center: fromLonLat([0,0]),
				  			constrainResolution:true
			   			})
		   			})
				})
			}
	}
}
</script>