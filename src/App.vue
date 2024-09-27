<script setup>
import { MapsComponent as EjsMaps, LayersDirective as ELayers, LayerDirective as ELayer, Marker, NavigationLine, Zoom } from '@syncfusion/ej2-vue-maps';
import { provide, createApp } from 'vue';
import { onMounted } from 'vue';
provide('maps', [Marker, NavigationLine, Zoom]);
const urlTemplate = 'https://tile.openstreetmap.org/level/tileX/tileY.png';
const markerSettings = [
      {
          visible: true,
          height: 20,
          width: 20,
          shape:'Image',
          imageUrl:'https://ej2.syncfusion.com/vue/demos/src/maps/images/ballon.png'
      }
    ];
    const navigationLine = [
     {
        visible:true,
        color: 'black',
        angle: 0,
        width: 2
     }
    ];
	const zoomSettings= {
        enable: true
    };
let Source = '';
let Destination = '';

const initMap = () => {
  const directionsService = new google.maps.DirectionsService();
  
  const onButtonClick = () => {
    Source = document.getElementById('input').value.toLowerCase();
    Destination = document.getElementById('output').value.toLowerCase();
    
    if (Source && Destination) {
      calculateAndDisplayRoute(directionsService);
    }
  };
  
  document.getElementById('route').addEventListener('click', onButtonClick);
};

const calculateAndDisplayRoute = (directionsService) => {
  directionsService.route({
    origin: { query: Source },
    destination: { query: Destination },
    travelMode: google.maps.TravelMode.DRIVING,
  }).then((response) => {
    const mapInstance = document.getElementById('container').ej2_instances[0];
    mapInstance.zoomSettings.shouldZoomInitially = true;
    const markers = mapInstance.layers[0].markerSettings[0];
    markers.dataSource.push({
      latitude: response.routes[0].legs[0].start_location.lat(),
      longitude: response.routes[0].legs[0].start_location.lng(),
    });
    markers.dataSource.push({
      latitude: response.routes[0].legs[0].end_location.lat(),
      longitude: response.routes[0].legs[0].end_location.lng(),
    });
    const navigationlines = mapInstance.layers[0].navigationLineSettings;
    const latLngs = response.routes[0].overview_path;
    const latitudes = latLngs.map(point => point.lat());
    const longitudes = latLngs.map(point => point.lng());    
    navigationlines[0].latitude = latitudes;
    navigationlines[0].longitude = longitudes;
  }).catch((e) => {
    window.alert('Directions request failed due to ' + e.message);
  });
};

onMounted(() => {
  initMap();
});
</script>
 
<template>

<div>
 <label for="textInput">Source: </label>
 <input type="text" id="input" name="textInput" /><br /><br />

 <label for="textOutput">Destination: </label>
 <input type="text" id="output" name="textOutput" /><br /><br />

 <button id="route">Add Route</button>
</div>
<div>
     <ejs-maps id="container" :zoomSettings='zoomSettings' width="700px" height="600px">
        <e-layers>
            <e-layer :urlTemplate= 'urlTemplate' :markerSettings='markerSettings' :navigationLineSettings='navigationLine'>
            </e-layer>
        </e-layers>
    </ejs-maps>
    </div>
</template>
 
<style scoped>
 
</style>