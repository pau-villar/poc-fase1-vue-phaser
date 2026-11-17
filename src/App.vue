<script setup>
import { ref } from 'vue';
// Importem el component pont que conté el <canvas> de Phaser
import PhaserCanvas from './components/PhaserCanvas.vue';

// Variable reactiva que guarda el nom escrit a la caixa de text
const nom = ref('Anna');
// Booleà per muntar/desmuntar el joc i verificar onMounted / onUnmounted
const mostrarJoc = ref(true);
// Guarda el color triat pel selector
const colorAvatar = ref('#10b981');
// Guarda l'estat triat al desplegable
const estat = ref('Disponible');
// Booleà que indica si el joc està en pausa. Comença en marxa (false)
const pausat = ref(false);

</script>

<template>
  <main class="contenidor-app">
    <!-- Tota la interfície d'usuari (inputs, botons) es fa en HTML/CSS dins de Vue -->
    <div class="controls">
      <input v-model="nom" placeholder="Escriu el teu nom d'usuari..." />
      <!-- En amagar/mostrar el llenç, tornem 'pausat' a false: el joc es crea
           de nou en marxa i així el text del botó de pausa sempre coincideix amb l'estat real -->
      <button @click="mostrarJoc = !mostrarJoc; pausat = false">
        {{ mostrarJoc ? 'Amaga el llenç' : 'Mostra el llenç' }}
      </button>
      <!-- v-model enllaça el selector amb 'colorAvatar': en triar un color,
           la variable canvia sola i Vue avisa als components que la fan servir -->
      <input type="color" v-model="colorAvatar" />

      <select v-model="estat">
        <option value="Disponible">Disponible</option>
        <option value="Ocupat">Ocupat</option>
        <option value="Absent">Absent</option>
      </select>
+      <button @click="pausat = !pausat">{{ pausat ? 'Reprèn' : 'Pausa' }}</button>
    </div>

    <!-- Passem 'nom' cap avall amb :nomUsuari i controlem el cicle amb v-if -->
    <PhaserCanvas v-if="mostrarJoc" :nomUsuari="nom" :colorAvatar="colorAvatar" :estat="estat" :pausat="pausat"/>
  </main>
</template>