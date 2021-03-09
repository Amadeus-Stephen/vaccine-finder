<template>
	<div ref="map-root" style="width:100%; height:100%">
	</div>
</template>
<script>
import 'ol/ol.css';
import GeoJSON from 'ol/format/GeoJSON';
import Map from 'ol/Map';
import View from 'ol/View';
import {OSM, Vector as VectorSource} from 'ol/source';
import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer';
import {fromLonLat} from 'ol/proj';



import "ol/ol.css"
let source = new VectorSource();
fetch('data/geojson/roads-seoul.geojson')
  .then(function (response) {
	return response.json();
  })
  .then(function (json) {
	var format = new GeoJSON();
	var features = format.readFeatures(json);
	var street = features[0];

	// convert to a turf.js feature
	var turfLine = format.writeFeatureObject(street);

	// show a marker every 200 meters
	var distance = 0.2;

	// get the line length in kilometers
	var length = turf.lineDistance(turfLine, 'kilometers');
	for (var i = 1; i <= length / distance; i++) {
	  var turfPoint = turf.along(turfLine, i * distance, 'kilometers');

	  // convert the generated point to a OpenLayers feature
	  var marker = format.readFeature(turfPoint);
	  marker.getGeometry().transform('EPSG:4326', 'EPSG:3857');
	  source.addFeature(marker);
	}

	street.getGeometry().transform('EPSG:4326', 'EPSG:3857');
	source.addFeature(street);
})
let vectorLayer = new VectorLayer({
  source: source,
});

let rasterLayer = new TileLayer({
  source: new OSM(),
});
let  coords = [0 , 0];
export default {
	name:"MapComtainer", 
	components: {},
   	beforeMount() {
 	  	let coords = [0 , 0];
  		// function success(position) {
		// 	const latitude  = position.coords.latitude;
		// 	const longitude = position.coords.longitude;
		// 	return [longitude, latitude]
 	 	// }
 	 	// function error() {
		// 	status.textContent = 'Unable to retrieve your location';
 		// }

 	 	// if(!navigator.geolocation) 
		// {
		//  	alert("Geoloaction is not supported in your browser")
 	 	// }
		// else
		// {
		// 	navigator.geolocation.getCurrentPosition((position) => 
		// 	{
		// 		coords = success(position)
		// 		console.log(coords)
		// 	}, error);
 		// }
		
		// function getPosition() {
    	// 	return new Promise((res, rej) => {
     	//    		navigator.geolocation.getCurrentPosition(res, rej);	


    	// 	}).then((data) => {
		// 		return [long , lat]
		// 	})
		// }
		let coordPromise = {then: function(resolve) {
			new Promise((res , rej) => {
				navigator.geolocation.getCurrentPosition(res, rej)
			}).then(data => {
            	let lat = data.coords.latitude
            	let long = data.coords.longitude
				resolve([long, lat])
			})	
		}}
		coords = Promise.resolve(coordPromise)
		console.log(Promise.resolve(coords))
	},
   	mounted() {
		new Map({
		   target: this.$refs["map-root"],
		   layers: [
			   rasterLayer , vectorLayer
		   ],
		   view: new View({
			   zoom:15,
			   center: fromLonLat(coords),
			   constrainResolution:true
		   	})
	   	})
   	}
}
</script>