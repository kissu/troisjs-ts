<template>
  <div style="position: relative;">
    <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
      <Camera ref="cameraC" :fov="15" :position="{ x: 0, y: 90, z: 100 }" />
      <Scene ref="sceneC" background="#111">
        <PointLight :position="{ y: 10, z: 50 }" color="#fff" :intensity="0.7" />
        <GltfModel ref="boxC" :rotation="{ y: Math.PI, z: 0 }" :scale="{ x: 0.5, y: 0.5, z: 0.5 }" src="./tron.gltf"
          @error="onError" />
      </Scene>
    </Renderer>
    <video ref="webcam" style="position: absolute; top: 0; right: 0; height: 20vh; width: 20vw;" :srcObject="videoSrc">
      nice meme</video>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { GltfModel, Box, Camera, PhongMaterial, Mesh, PlaneGeometry, PointLight, Renderer, Scene } from 'troisjs'
import { GridHelper, InstancedMesh } from 'three'
import * as pose from '@tensorflow-models/pose-detection'
import '@tensorflow/tfjs-backend-webgl';

const sleep = async (amount) => await new Promise((resolve) => setTimeout(resolve, amount))

const cameraC = ref()
const rendererC = ref()
const sceneC = ref()
const webcam = ref()
let videoSrc = ref()

let currentDirectionIndex = ref(0)

const directions = [
  { axis: 'X', multiplier: 1, name: 'front' },
  { axis: 'Z', multiplier: 1, name: 'right' },
  { axis: 'X', multiplier: -1, name: 'bottom' },
  { axis: 'Z', multiplier: -1, name: 'left' }
]
const getCurrentDirection = (index) => directions.at(index % 4)

const onError = (e) => {
  console.log('error', e)
}

onMounted(async () => {
  const renderer = rendererC.value
  const camera = cameraC.value.camera
  const scene = sceneC.value.scene
  const moto = scene.children[2]

  const videoConfig = {
    audio: false,
    video: {
      facingMode: 'user',
      width: 320,
      height: 240,
      frameRate: {
        ideal: 60,
      }
    }
  }
  const stream = await navigator.mediaDevices.getUserMedia(videoConfig)
  videoSrc.value = stream
  await webcam.value.load()
  webcam.value.play()

  const model = pose.SupportedModels.MoveNet;
  const detectorConfig = {
    // modelType: poseDetection.movenet.modelType.MULTIPOSE_LIGHTNING,
    enableTracking: true,
    // trackerType: poseDetection.TrackerType.BoundingBox
  }
  const detector = await pose.createDetector(model, detectorConfig);
  // await sleep(2000)

  const grid = new GridHelper(40, 40, 0x00A8C9, 0x00A8C9)
  scene.add(grid)

  renderer.onBeforeRender(async () => {
    scene.children[2]['translateZ'](0.02)
    //! directions
    // +x front -x bottom
    // -z left +z right
    // const poses = await detector.estimatePoses(webcam.value)
    // const leftWrist = poses[0].keypoints[9].y
    // const rightWrist = poses[0].keypoints[10].y
    // const direction = leftWrist / rightWrist
    // console.log('d', direction)
    // switch (true) {
    //   case (direction > 2):
    //     console.log('turn left')
    //     break;
    //   case (direction < 0.5):
    //     console.log('turn right')
    //     break;
    //   default:
    //     console.log('straight')
    //     break;
    // }
  })

  document.addEventListener('keydown', (e) => {
    e.preventDefault()
    if (e.key === 'ArrowLeft') {
      sceneC.value.scene.children[2].rotation.y += Math.PI / 2
      currentDirectionIndex.value--
    } else if (e.key === 'ArrowRight') {
      sceneC.value.scene.children[2].rotation.y -= Math.PI / 2
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
