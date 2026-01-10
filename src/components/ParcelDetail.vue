<script setup>
defineProps({
  parcel: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['close']);
</script>

<template>
  <div class="parcel-detail">
    <div class="header">
      <h3>Détails de la parcelle</h3>
      <button @click="$emit('close')">×</button>
    </div>
    
    <div v-if="parcel">
      <p><strong>Propriétaire :</strong> {{ parcel.owner }}</p>
      <p><strong>Surface :</strong> {{ parcel.area }} m²</p>
      <p><strong>Type :</strong> {{ parcel.type }}</p>
      <p><strong>ID :</strong> {{ parcel.id }}</p>
    </div>
  </div>
</template>

<style scoped>
.parcel-detail {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 300px;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.2);
  z-index: 1000; /* Ensure it's above the map */
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1em;
}

button {
  background: none;
  border: none;
  font-size: 1.5em;
  cursor: pointer;
}
</style>
