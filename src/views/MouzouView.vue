<script setup>
import { ref } from 'vue'

const name = ref('')
</script>

<template>
  <h1 class="title">Bonjour {{ name || 'inconnu' }}</h1>

  <input
    class="input"
    v-model="name"
    placeholder="Mettez votre nom ici"
  />
</template>

<style>
.title {
  position: absolute;
  top: 10px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  color: white;
}

.input {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 1000;
}
</style>
