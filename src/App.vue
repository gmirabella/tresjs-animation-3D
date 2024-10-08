<script setup lang="ts">
import { TresCanvas } from '@tresjs/core'
import { ref, watchEffect } from 'vue'
import { Pane } from 'tweakpane'
import { useRenderLoop } from '@tresjs/core'
import { shallowRef } from 'vue'
import { Path } from 'three'
// useTweakPane() serve per creare una finestra di modifica dei parametri

const pane = new Pane()
const { onLoop } = useRenderLoop()
/*
 se uso rotation per animare oggetti più complessi, andrà a scatti per questo
  è meglio usare TemplateRef per animare gli oggetti

const rotationY = ref(0)
const rotationZ = ref(0)
onLoop(() => {
  rotationY.value += 0.01
  rotationZ.value += 0.02
})
*/

const diceRef = shallowRef() // shallowRef è un ref che non tiene conto delle modifiche ai figli è più performante e più smooth l'animazione

onLoop(({ delta, elapsed }) => {
  if (diceRef.value) {
    diceRef.value.rotation.y += delta
    diceRef.value.rotation.z = elapsed * 0.02

    diceRef.value.position.y = Math.sin(elapsed * 2)
    diceRef.value.scale.set(Math.sin(elapsed * 2), Math.sin(elapsed * 2), Math.sin(elapsed * 2))
  }
})

//cubeRef ci torna l'oggetto mesh
watchEffect(() => {
  console.log(diceRef.value)
})
</script>

<template>
  <TresCanvas clear-color="#506665">
    <TresPerspectiveCamera :position="[0, 4, 4]" :look-at="[0, 0, 0]" />
    <TresMesh ref="diceRef" :position="[0, 0, 0]" :scale="[1, 1, 1]">
      <!-- <TresBoxGeometry :args="[1, 1, 1]" /> cube  -->
      <TresIcosahedronGeometry :args="[1, 0]" />
      <TresMeshNormalMaterial />
    </TresMesh>
    <!-- <TresAxesHelper /> -->
  </TresCanvas>
</template>

<!-- -- quello che avrei dovuto fare in three.js
const scene = new THREE.Scene(); 
const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const mesh = new THREE.Mesh(geometry, material);
scene.add(mesh); 
const camera = new THREE.PerspectiveCamera(75,window.innerWidth / window.innerHeight, 0.1, 1000); 
camera.position.z = 5;const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
-->

<!-- in tresjs non bisogna pensare al render come in three.js ma basta importare il componente TresCanvas e il gioco è fatto -->
<!-- in tres.js posso usare gli args per passare le props -->
<!-- look-at serve per dire dove sta guardando la camera e position per dire dove sta la camera -->

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
