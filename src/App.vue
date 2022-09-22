<template>
  <div @click="playMusic">
    <audio hidden="true" ref="audio">
      <source src="./synthwave.mp3" type="audio/mpeg">
    </audio>
    <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
      <Camera ref="cameraC" :fov="15" :position="{ x: 0, y: 90, z: 100 }" />
      <Scene ref="sceneC" background="#111">
        <PointLight :position="{ y: 10, z: 50 }" color="#fff" :intensity="0.7" />
        <GltfModel ref="boxC" :rotation="{ y: Math.PI, z: 0 }" :scale="{ x: 0.5, y: 0.5, z: 0.5 }" src="./tron.gltf"
          @error="onError" />
      </Scene>
    </Renderer>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import { GltfModel, Box, Camera, PhongMaterial, Mesh, PlaneGeometry, PointLight, Renderer, Scene } from 'troisjs'
import { GridHelper, InstancedMesh } from 'three'
import { useGamepad } from '@vueuse/core'

const cameraC = ref()
const rendererC = ref()
const sceneC = ref()
const audio = ref()
const { gamepads } = useGamepad()

watch(() => gamepads, (newPad) => {
  sceneC.value.scene.children[2].rotation.y -= newPad.value?.[0]?.axes?.[0] * Math.PI / 100
}, {
  deep: true,
})

const onError = (e) => {
  console.log('error', e)
}

const playMusic = () => {
  audio.value.play()
}

onMounted(() => {
  const renderer = rendererC.value
  const camera = cameraC.value.camera
  const scene = sceneC.value.scene
  const moto = scene.children[2]

  const grid = new GridHelper(40, 40, 0x00A8C9, 0x00A8C9)
  scene.add(grid)

  renderer.onBeforeRender(() => {
    scene.children[2]['translateZ'](0.04)
    //! directions
    // +x front -x bottom
    // -z left +z right
  })

  document.addEventListener('keydown', (e) => {
    e.preventDefault()
    if (e.key === 'ArrowLeft') {
      sceneC.value.scene.children[2].rotation.y += Math.PI / 2
    } else if (e.key === 'ArrowRight') {
      sceneC.value.scene.children[2].rotation.y -= Math.PI / 2
    }
  });
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
