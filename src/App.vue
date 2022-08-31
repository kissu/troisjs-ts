<template>
  <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
    <Camera ref="cameraC" :fov="30" :look-at="{ x: 0 }" :position="{ x: 0, y: 3, z: 50 }" />
    <Scene ref="sceneC" background="#333">
      <PointLight :position="{ y: 10, z: 50 }" color="#fff" :intensity="0.7" />
      <PointLight :position="{ y: -10, z: -50 }" color="#fff" :intensity="0.7" />

      <Mesh ref="meshC" :position="{ x: 0, y: 0}" :rotation="{ z: 0, x: 90 }">
        <PlaneGeometry :width="40" :height="40" />
      </Mesh>
      <Box :size="1">
        <PhongMaterial color="#FF5F15" />
      </Box>
    </Scene>
  </Renderer>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { onKeyStroke } from '@vueuse/core'

import { Box, Camera, PhongMaterial, Mesh, PlaneGeometry, PointLight, Renderer, Scene } from 'troisjs'
import { GridHelper, DoubleSide } from 'three'

const cameraC = ref()
const rendererC = ref()
const meshC = ref()
const sceneC = ref()

onMounted(() => {
  const renderer = rendererC.value
  const mesh = meshC.value.mesh
  const camera = cameraC.value.camera
  const scene = sceneC.value.scene

  const grid = new GridHelper(40, 40)
  scene.add(grid)
  console.log('camera', camera)

  renderer.onBeforeRender(() => {
    mesh.rotation.z += 0.02
    camera.rotation.x += 0.02
  })
  // console.log('c', cameraC.value.camera.rotation)
  onKeyStroke('ArrowDown', (e) => {
    e.preventDefault()
    camera.rotation.z += 0.2
    console.log('nice')
  })
})

</script>

<style>
body {
  margin: 0;
}
canvas {
  display: block;
}
</style>
