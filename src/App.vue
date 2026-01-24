<<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <div class="layout">
    <nav class="nav">
      <router-link to="/">Home</router-link>
      <router-link to="/about">About</router-link>
      <router-link to="/mou">Mouzou</router-link>
    </nav>

    <main class="content">
      <RouterView />
    </main>
  </div>
</template>

<style>
html, body, #app {
  height: 100%;
  margin: 0;
}

/* Layout global */
.layout {
  display: flex;
  flex-direction: column;
  height: 100%;
}

/* Menu */
.nav {
  background: #222;
  padding: 10px;
}

.nav a {
  color: white;
  margin-right: 15px;
  text-decoration: none;
}

/* Zone des pages */
.content {
  flex: 1; /* prend tout l’espace restant */
  position: relative;
}
</style>
