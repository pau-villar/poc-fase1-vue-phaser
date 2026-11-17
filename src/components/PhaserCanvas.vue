<script setup>
// 1. Importem ref (referència al DOM) i els hooks del cicle de vida de Vue 3
import { ref,watch, onMounted, onUnmounted } from 'vue'; // [NOU] Importar watch
// 2. Importem el motor Phaser 4 i la nostra escena personalitzada
import Phaser from 'phaser';
import { MainScene } from '../game/MainScene';

// [NOU] Rep la prop des d'App.vue
const props = defineProps(['nomUsuari', 'colorAvatar', 'estat', 'pausat']);

const contenidor = ref(null); // Apuntarà al <div ref="contenidor"> de sota
let game = null;              // Guardem la instància per destruir-la en sortir

// S'executa un sol cop quan el <div> ja existeix a la pàgina
onMounted(() => {
  game = new Phaser.Game({
    type: Phaser.AUTO,          // Tria WebGL automàticament (o Canvas 2D)
    width: 640, height: 400,    // Mides del llenç en píxels
    backgroundColor: '#1e293b', // Fons fosc per distingir l'àrea del joc
    parent: contenidor.value,   // Injecta el <canvas> dins del nostre <div>
    scene: [MainScene]          // Escena inicial que arrencarà el motor
  });

  // [NOU] Sincronitza el nom actual només acabar d'arrencar (útil amb v-if)
  game.events.once('ready', () => {
    // Guardem l'escena en una constant per no repetir getScene() dues vegades
    const escena = game?.scene.getScene('MainScene');
    escena?.actualitzarNom(props.nomUsuari);
    // Apliquem també el color actual. Cal perquè, si amagues i tornes a
    // mostrar el llenç, Phaser es crea de nou amb l'avatar verd per defecte
    escena?.canviarColor(props.colorAvatar);
    // Apliquem també l'estat actual, pel mateix motiu que el color
    escena?.actualitzarEstat(props.estat);
  });

});

// IMPRESCINDIBLE: quan s'amaga (v-if="false"), atura el bucle a 60 FPS
onUnmounted(() => game?.destroy(true));

// [NOU] Al MATEIX nivell que onMounted (MAI dins d'onMounted)
watch(() => props.nomUsuari, (nouNom) => {
    const escena = game?.scene.getScene('MainScene');
    escena?.actualitzarNom(nouNom);
});

// Segon watch: vigila la prop 'colorAvatar'. Cada cop que l'usuari tria un
// color nou a App.vue, aquesta funció s'executa i li passa el color a l'escena de Phaser
watch(() => props.colorAvatar, (nouColor) => {
    game?.scene.getScene('MainScene')?.canviarColor(nouColor);
});

// Tercer watch: cada cop que canvia el desplegable, actualitza el text a Phaser
watch(() => props.estat, (nouEstat) => {
    game?.scene.getScene('MainScene')?.actualitzarEstat(nouEstat);
});

// Quart watch: vigila la prop 'pausat'. Si és true, atura l'escena amb
// scene.pause() (update() deixa d'executar-se i l'avatar es queda quiet); si és false,
// la reactiva amb scene.resume()
watch(() => props.pausat, (p) => {
    const escena = game?.scene.getScene('MainScene');
    p ? escena?.scene.pause() : escena?.scene.resume();
});

</script>
<template><div ref="contenidor"></div></template> <!-- Aquí s'injecta el <canvas> -->