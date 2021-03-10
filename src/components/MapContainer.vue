<template>
	<div ref="map-root" style="width:100%; height:100%">
	</div>
	
</template>
<script>
import 'ol/ol.css';
import Map from 'ol/Map';
import View from 'ol/View';
import Feature from 'ol/Feature'
import Polygon from "ol/geom/Polygon"
import Geocoder from "ol-geocoder";
import {OSM, Vector as VectorSource} from 'ol/source';
import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer';
import {fromLonLat, transform} from 'ol/proj';



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
		
			 navigator.geolocation.getCurrentPosition((data)=>{
				 console.log("return")
				 console.log(data)
				 console.log(data.coords.longitude , data.coords.latitude)
				fetch(`https://api.bigdatacloud.net/data/reverse-geocode-client\?latitude\=${data.coords.latitude}\&longitude\=${data.coords.longitude}\&localityLanguage\=en`)
				.then(response => response.json())
				.then(geodata => console.log(geodata))
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
				var thing = new Polygon( [[
   					transform([-16,-22], 'EPSG:4326', 'EPSG:3857'),
    				transform([-44,-55], 'EPSG:4326', 'EPSG:3857'),
    				transform([-88,75], 'EPSG:4326', 'EPSG:3857')
				]]);
				var featurething = new Feature({
    				name: "Thing",
    				geometry: thing
				});
				source.addFeature( featurething );
				//Instantiate with some options and add the Control
				let geocoder = new Geocoder('nominatim', {
				  provider: 'osm',
				  lang: 'en',
				  placeholder: 'Search for ...',
				  targetType: 'text-input',
				  limit: 5,
				  debug: false,
				  autoComplete: true,
				  keepOpen: true
				});
				map.addControl(geocoder);
				
				//Listen when an address is chosen
				geocoder.on('addresschosen', function (evt) {
					console.info(evt);
				});
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
</script>