<script setup>
import { ref, computed } from 'vue';
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LPolygon, LWmsTileLayer } from "@vue-leaflet/vue-leaflet";
import ParcelDetail from '../components/ParcelDetail.vue';

const zoom = ref(19); // Zoomed in closer for cadastral data
const center = ref([6.2340, 1.2038]); 

const selectedParcel = ref(null);
const filterStatus = ref('all'); 

// GeoServer Configuration
const geoserverUrl = "https://monticulate-superingeniously-marge.ngrok-free.dev/geoserver/DCCF/wms";
const wmsLayerOptions = {
  layers: "DCCF:parcelles_ag1", // Added namespace just to be safe
  format: "image/png",
  transparent: true,
  version: '1.1.1',
  attribution: "geoserver",
  maxZoom: 22,
  tiled: true, // Often improves rendering with GeoServer
  styles: '' // Use default style
};


const parcels = ref([
  {
    id: 1,
    latlngs: [
      [6.2340, 1.2038],
      [6.2350, 1.2038],
      [6.2350, 1.2048],
      [6.2340, 1.2048]
    ],
    owner: 'Jean Dupont',
    area: 1200,
    type: 'Résidentiel',
    status: 'sold' // Level 4: Status
  },
  {
    id: 2,
    latlngs: [
      [6.2320, 1.2020],
      [6.2330, 1.2010],
      [6.2335, 1.2025]
    ],
    owner: 'Marie Curie',
    area: 850,
    type: 'Espace Vert',
    status: 'for_sale'
  },
  {
    id: 3,
    latlngs: [
       [6.2360, 1.2050],
       [6.2370, 1.2050],
       [6.2370, 1.2070],
       [6.2360, 1.2060]
    ],
    owner: 'Ville de Lomé',
    area: 2500,
    type: 'Public',
    status: 'sold'
  },
  {
    id: 4,
    latlngs: [
      [6.2337,  1.2054],
      [6.2347,  1.2061],
      [6.2332,  1.2080],
      [6.2318,  1.2058]
    ],
    owner: 'MOUZOU payodena',
    area: 2000,
    type: 'Public',
    status: 'essi'
  }
]);

// Level 4: Dynamic coloring based on status
const getParcelColor = (status) => {
  if (status === 'sold') return 'red';
  if (status === 'for_sale') return 'green';
  if (status === 'essi') return 'orange';
  return 'blue';
};

// Level 4: Filtering
const filteredParcels = computed(() => {
  if (filterStatus.value === 'all') {
    return parcels.value;
  }
  return parcels.value.filter(p => p.status === filterStatus.value);
});

const selectParcel = (parcel) => {
  selectedParcel.value = parcel;
};

const closeDetail = () => {
  selectedParcel.value = null;
};
</script>

<template>
  <main style="height: 100vh; width: 100vw; position: relative;">
    
    <!-- Level 4: Filter Controls -->
    <div class="controls">
      <label>Filtrer par statut : </label>
      <select v-model="filterStatus">
        <option value="all">Tout voir</option>
        <option value="for_sale">A vendre (Vert)</option>
        <option value="sold">Vendu (Rouge)</option>
        <option value="essi">Essi (orange)</option>
      </select>
    </div>

    <l-map ref="map" v-model:zoom="zoom" v-model:center="center" :useGlobalLeaflet="false">
      <!-- Base Layer (OpenStreetMap) -->
      <l-tile-layer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        layer-type="base"
        name="OpenStreetMap"
      ></l-tile-layer>

      <!-- GeoServer WMS Layer -->
      <l-wms-tile-layer
        :url="geoserverUrl"
        :layers="wmsLayerOptions.layers"
        :format="wmsLayerOptions.format"
        :transparent="wmsLayerOptions.transparent"
        :version="wmsLayerOptions.version"
        :styles="wmsLayerOptions.styles"
        :tiled="wmsLayerOptions.tiled"
        :attribution="wmsLayerOptions.attribution"
        :max-zoom="wmsLayerOptions.maxZoom"
      ></l-wms-tile-layer>
      
      <!-- Vector Polygons (Mock Data) -->
      <l-polygon
        v-for="parcel in filteredParcels"
        :key="parcel.id"
        :lat-lngs="parcel.latlngs"
        :color="getParcelColor(parcel.status)"
        :fill="true"
        :fillOpacity="0.5"
        @click="selectParcel(parcel)"
      />
    </l-map>

    <ParcelDetail 
      v-if="selectedParcel" 
      :parcel="selectedParcel" 
      @close="closeDetail"
    />
  </main>
</template>

<style scoped>
.controls {
  position: absolute;
  top: 20px;
  left: 60px; /* Offset to avoid zoom controls */
  background: white;
  padding: 10px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.2);
  z-index: 1000;
}
</style>


