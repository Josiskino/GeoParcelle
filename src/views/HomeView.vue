<script setup>
import { ref, computed } from 'vue';
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LPolygon } from "@vue-leaflet/vue-leaflet";
import ParcelDetail from '../components/ParcelDetail.vue';

const zoom = ref(15);
const center = ref([6.1319, 1.2228]); // Lomé 

const selectedParcel = ref(null);
const filterStatus = ref('all'); // Level 4: Filter state

const parcels = ref([
  {
    id: 1,
    latlngs: [
      [6.1319, 1.2228],
      [6.1329, 1.2228],
      [6.1329, 1.2238],
      [6.1319, 1.2238]
    ],
    owner: 'Jean Dupont',
    area: 1200,
    type: 'Résidentiel',
    status: 'sold' // Level 4: Status
  },
  {
    id: 2,
    latlngs: [
      [6.1300, 1.2210],
      [6.1310, 1.2200],
      [6.1315, 1.2215]
    ],
    owner: 'Marie Curie',
    area: 850,
    type: 'Espace Vert',
    status: 'for_sale'
  },
  {
    id: 3,
    latlngs: [
       [6.1340, 1.2240],
       [6.1350, 1.2240],
       [6.1350, 1.2260],
       [6.1340, 1.2250]
    ],
    owner: 'Ville de Lomé',
    area: 2500,
    type: 'Public',
    status: 'sold'
  }
]);

// Level 4: Dynamic coloring based on status
const getParcelColor = (status) => {
  if (status === 'sold') return 'red';
  if (status === 'for_sale') return 'green';
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
      </select>
    </div>

    <l-map ref="map" v-model:zoom="zoom" v-model:center="center" :useGlobalLeaflet="false">
      <l-tile-layer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        layer-type="base"
        name="OpenStreetMap"
      ></l-tile-layer>
      
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
