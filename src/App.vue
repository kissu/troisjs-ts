<template>
  <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
    <Camera :fov="15" :position="{ x: 0, y: 90, z: 100 }" />
    <Scene ref="sceneC">
      <GltfModel :rotation="{ y: Math.PI, z: 0 }"
        :scale="{ x: 0.5, y: 0.5, z: 0.5 }"
        src="./tron.gltf"
        @error="onError"
      />
    </Scene>
  </Renderer>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { GltfModel, Camera, Renderer, Scene } from 'troisjs'
import { GridHelper } from 'three'

const rendererC = ref()
const sceneC = ref()

const onError = (e) => {
  console.log('error', e)
}

onMounted(() => {
  const renderer = rendererC.value
  const scene = sceneC.value.scene

  const grid = new GridHelper(40, 40, 0x00A8C9, 0x00A8C9)
  scene.add(grid)

  renderer.onBeforeRender(() => {
    scene.children[2]['translateZ'](0.02)
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
