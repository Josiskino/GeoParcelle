<script setup>
import { computed } from 'vue';

const props = defineProps({
  parcel: {
    type: Object,
    required: true,
  },
});

defineEmits(['close']);

// Libellés lisibles pour les colonnes connues. Toute autre clé est
// affichée telle quelle (utile si on ajoute des colonnes plus tard).
const LABELS = {
  // parcelles_ag1 (PostGIS / GeoServer)
  nup: 'Numéro NUP',
  nom_sectio: 'Section',
  ilot_id: 'Îlot',
  parc_num: 'N° de parcelle',
  shape_area: 'Surface (m²)',
  shape_leng: 'Périmètre (m)',
  gid: 'GID',
  objectid_1: 'Object ID',
  // mock data
  owner: 'Propriétaire',
  area: 'Surface (m²)',
  type: 'Type',
  id: 'ID',
  status: 'Statut',
};

// Clés à ne pas afficher (ex. coordonnées brutes du polygon mock).
const HIDDEN_KEYS = new Set(['latlngs', 'geometry', 'bbox']);

const formatLabel = (key) => LABELS[key] ?? key;

const formatValue = (value) => {
  if (value === null || value === undefined || value === '') return '—';
  if (typeof value === 'number') {
    return Number.isInteger(value) ? value.toString() : value.toFixed(2);
  }
  return String(value);
};

const visibleEntries = computed(() =>
  Object.entries(props.parcel).filter(([key]) => !HIDDEN_KEYS.has(key)),
);

const title = computed(() => {
  const p = props.parcel;
  if (p.nup) return `Parcelle ${p.nup}`;
  if (p.id) return `Parcelle #${p.id}`;
  return 'Détails de la parcelle';
});
</script>

<template>
  <div class="parcel-detail">
    <div class="header">
      <h3>{{ title }}</h3>
      <button class="close-btn" aria-label="Fermer" @click="$emit('close')">×</button>
    </div>

    <div class="props">
      <div v-for="[key, value] in visibleEntries" :key="key" class="prop-row">
        <span class="prop-label">{{ formatLabel(key) }}</span>
        <span class="prop-value">{{ formatValue(value) }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.parcel-detail {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 320px;
  max-height: calc(100vh - 40px);
  overflow-y: auto;
  background: white;
  padding: 16px 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.15);
  z-index: 1000;
  font-family: system-ui, -apple-system, sans-serif;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  padding-bottom: 8px;
  border-bottom: 1px solid #eee;
}

.header h3 {
  margin: 0;
  font-size: 1rem;
  color: #1a1a1a;
}

.close-btn {
  background: none;
  border: none;
  font-size: 1.6rem;
  line-height: 1;
  cursor: pointer;
  color: #888;
  padding: 0 4px;
}

.close-btn:hover {
  color: #1a1a1a;
}

.props {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.prop-row {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  font-size: 0.875rem;
  padding: 4px 0;
  border-bottom: 1px dashed #f0f0f0;
}

.prop-row:last-child {
  border-bottom: none;
}

.prop-label {
  color: #666;
  font-weight: 500;
  flex-shrink: 0;
}

.prop-value {
  color: #1a1a1a;
  font-family: 'SF Mono', Monaco, Consolas, monospace;
  text-align: right;
  word-break: break-word;
}
</style>
