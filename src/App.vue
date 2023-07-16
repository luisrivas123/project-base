<script setup>
const name = 'Vue dinámico'
const styleColor = 'color: blue'
const arrayColors = ['blue', 'red', 'peru']
const activo = false
</script>

<template>
  <h1>Hola {{ name.toUpperCase() }}</h1>
  <h2 :style="styleColor">Soy azul</h2>
  <h2 :style="`color: ${arrayColors[2]}`">soy color</h2>
  <h2>
    {{ activo ? 'Estoy activo' : 'Estoy inactivo' }}
  </h2>
  <h2 v-if="activo">Estoy Activo</h2>
  <p v-else>Estoy Inactivo</p>
</template>

<style>
h1 {
  color: red;
}
</style>