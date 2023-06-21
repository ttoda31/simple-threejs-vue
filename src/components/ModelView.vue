<script setup lang="ts">
import * as THREE from "three"
// import { FirstPersonControls } from "three/addons/controls/FirstPersonControls.js"
import { OrbitControls } from "three/addons/controls/OrbitControls.js"
import { DRACOLoader } from "three/addons/loaders/DRACOLoader.js"
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js"
import { onUnmounted } from "vue"

const scene = new THREE.Scene()
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  2000
)

const renderer = new THREE.WebGLRenderer()
renderer.setPixelRatio(1)
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

// const controls = new FirstPersonControls(camera, renderer.domElement)
const controls = new OrbitControls(camera, renderer.domElement)

const hemiLight = new THREE.HemisphereLight(0xffffff, 0xffffff, 0.4)
// hemiLight.color.set(0xff6666)
// hemiLight.groundColor.set(0xfffe88)
hemiLight.position.set(0, 30, 30)
scene.add(hemiLight)
const hemiLightHelper = new THREE.HemisphereLightHelper(hemiLight, 10)
scene.add(hemiLightHelper)

const dirLight = new THREE.DirectionalLight(0xffffff, 1)
dirLight.position.set(50, 80, 50)
dirLight.position.multiplyScalar(30)
scene.add(dirLight)
dirLight.castShadow = true
dirLight.shadow.mapSize.width = 2048
dirLight.shadow.mapSize.height = 2048
const d = 50
dirLight.shadow.camera.right = d
dirLight.shadow.camera.top = d
dirLight.shadow.camera.bottom = -d
dirLight.shadow.camera.far = 3500
dirLight.shadow.bias = -0.0001
const dirLightHelper = new THREE.DirectionalLightHelper(dirLight, 10)
scene.add(dirLightHelper)

const loader = new GLTFLoader()
const useDraco = false

let modelUrl = "models/example.glb"
if (useDraco) {
  modelUrl = "models/example_Draco.glb"
  const DracoLoader = new DRACOLoader()
  DracoLoader.setDecoderPath("draco/")
  loader.setDRACOLoader(DracoLoader)
}

let model = undefined
loader.load(
  modelUrl,
  function (gltf) {
    model = gltf.scene
    model.scale.set(1.0, 1.0, 1.0)
    model.position.set(0, 0, 0)
    scene.add(model)
    console.log("loaded")
  },
  function (progress) {
    const percent = progress.loaded / progress.total
    console.log(`Loading model: ${Math.round(percent * 100)}%`)
  },
  function (error) {
    console.log("An error happened")
    console.error(error)
  }
)

camera.position.z = 5

// const clock = new THREE.Clock()
function animate() {
  controls.update()
  renderer.render(scene, camera)
  requestAnimationFrame(animate)
}
animate()

onResize()
window.addEventListener("resize", onResize)
function onResize() {
  renderer.setPixelRatio(window.devicePixelRatio)
  const width = window.innerWidth
  const height = window.innerHeight
  renderer.setSize(width, height)

  camera.aspect = width / height
  camera.updateProjectionMatrix()
}

onUnmounted(() => {
  window.removeEventListener("resize", onResize)
})
</script>

<template></template>
