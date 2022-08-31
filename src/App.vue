<template>
  <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
    <Camera ref="cameraC" :fov="15" :position="{ x: 0, y: 90, z: 100 }" />
    <Scene ref="sceneC" background="#111">
      <PointLight :position="{ y: 10, z: 50 }" color="#fff" :intensity="0.7" />

      <!-- <Mesh ref="meshC" :position="{ x: 0, y: 0}" :rotation="{ z: 0, x: 90 }">
        <PlaneGeometry :width="40" :height="40" />
      </Mesh> -->
      <Box ref="boxC" :scale="{ x: 5, y: 2, z: 1 }" :rotation="{ x: Math.PI / 2, y: Math.PI / 2, z: -Math.PI / 2 }">
        <!-- :rotation="{ x: Math.PI / 2, y: Math.PI / 2, z: -Math.PI / 2 }" -->
        <PhongMaterial color="#FF5F15" />
      </Box>
    </Scene>
  </Renderer>
</template>

<script setup>
import { ref, onMounted } from 'vue'
// import { onKeyStroke } from '@vueuse/core'
import { Box, Camera, PhongMaterial, Mesh, PlaneGeometry, PointLight, Renderer, Scene } from 'troisjs'
import { GridHelper } from 'three'

const cameraC = ref()
const rendererC = ref()
// const meshC = ref()
const sceneC = ref()
const boxC = ref()

let currentDirectionIndex = ref(0)

const directions = [
  { axis: 'X', multiplier: 1, name: 'front' },
  { axis: 'Z', multiplier: 1, name: 'right' },
  { axis: 'X', multiplier: -1, name: 'bottom' },
  { axis: 'Z', multiplier: -1, name: 'left' }
]
const getCurrentDirection = (index) => directions.at(index % 4)

onMounted(() => {
  const renderer = rendererC.value
  // const orbitControls = renderer.three.cameraCtrl
  // const mesh = meshC.value.mesh
  const camera = cameraC.value.camera
  const scene = sceneC.value.scene
  const box = boxC.value.mesh

  const grid = new GridHelper(40, 40, 0x00A8C9, 0x00A8C9)
  scene.add(grid)
  // console.log('eh', orbitControls)
  // orbitControls.enablePan = false
  // camera.rotation.x = 220
  // camera.lookAt(box.position.x, box.position.y, box.position.z)

  renderer.onBeforeRender(() => {
    // console.log('ee', getCurrentDirection(currentDirectionIndex.value).name)

    const getCurrent = getCurrentDirection(currentDirectionIndex.value)
    console.log('cur', getCurrent)
    box[`translate${getCurrent.axis}`](0.02 * getCurrent.multiplier)
    // box['translateZ'](0.02)
    // box.translateOnAxis(box.position.y, 0.02)
    //! get direction
    // +x front -x bottom
    // -z left +z right


    // camera.rotation.x += 90 * Math.PI / 180
    // orbitControls.object.rotation.y += 90
    // camera.lookAt({ x: 0, y: 12, z: 7})
    // camera.rotation.x += 90
    // orbitControls.object.rotation.y += 90
    // orbitControls.update()
    // renderer.render(scene, camera)
  })

  document.addEventListener('keydown', (e) => {
    e.preventDefault()
    if (e.key === 'ArrowLeft') {
      // box.rotateY(45 * (Math.PI / 2))
      currentDirectionIndex.value--
    } else if (e.key === 'ArrowRight') {
      // box.rotateY(-45 * (Math.PI / 2))
      currentDirectionIndex.value++
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
