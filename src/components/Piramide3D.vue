<template>
  <div class="piramide-container" ref="container"></div>
</template>

<script setup lang="ts">
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { ref, onMounted, onBeforeUnmount } from 'vue'

const container = ref<HTMLDivElement | null>(null)

// Variables para Three.js
let scene: THREE.Scene
let camera: THREE.PerspectiveCamera
let renderer: THREE.WebGLRenderer
let pyramid: THREE.Group
let controls: OrbitControls

const initThree = () => {
  // Crear la escena
  scene = new THREE.Scene()
  
  // Configurar la cámara
  camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  )
  camera.position.z = 5

  // Configurar el renderer
  renderer = new THREE.WebGLRenderer({ antialias: true })
  const containerWidth = container.value?.clientWidth || window.innerWidth / 2
  const containerHeight = container.value?.clientHeight || window.innerHeight
  renderer.setSize(containerWidth, containerHeight)
  renderer.setClearColor(0x000000)
  container.value?.appendChild(renderer.domElement)

  // Crear la pirámide
  const geometry = new THREE.ConeGeometry(1.5, 2, 4)
  
  // Material para los vértices
  const pointsMaterial = new THREE.PointsMaterial({
    color: 0x0000ff,
    size: 0.1
  })
  
  // Material para las líneas
  const lineMaterial = new THREE.LineBasicMaterial({
    color: 0x0000ff
  })

  // Crear los puntos (vértices)
  const points = new THREE.Points(geometry, pointsMaterial)
  scene.add(points)

  // Crear las líneas
  const edges = new THREE.EdgesGeometry(geometry)
  const lines = new THREE.LineSegments(edges, lineMaterial)
  scene.add(lines)

  // Grupo para mantener todo junto
  pyramid = new THREE.Group()
  pyramid.add(points)
  pyramid.add(lines)
  scene.add(pyramid)

  // Añadir luces
  const light = new THREE.DirectionalLight(0xffffff, 1)
  light.position.set(1, 1, 1)
  scene.add(light)

  const ambientLight = new THREE.AmbientLight(0x404040)
  scene.add(ambientLight)

  // Añadir controles de órbita
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
}

const animate = () => {
  requestAnimationFrame(animate)
  
  if (pyramid && controls) {
    // Rotar la pirámide
    pyramid.rotation.x += 0.01
    pyramid.rotation.y += 0.01

    // Actualizar controles
    controls.update()

    // Renderizar la escena
    renderer.render(scene, camera)
  }
}

const onWindowResize = () => {
  if (camera && renderer && container.value) {
    const containerWidth = container.value.clientWidth
    const containerHeight = container.value.clientHeight
    camera.aspect = containerWidth / containerHeight
    camera.updateProjectionMatrix()
    renderer.setSize(containerWidth, containerHeight)
  }
}

const dispose = () => {
  if (renderer) {
    window.removeEventListener('resize', onWindowResize)
    renderer.dispose()
    scene.traverse((object: THREE.Object3D) => {
      if (object instanceof THREE.Mesh) {
        if (object.geometry) {
          object.geometry.dispose()
        }
        if (object.material) {
          if (Array.isArray(object.material)) {
            object.material.forEach((material: THREE.Material) => material.dispose())
          } else {
            object.material.dispose()
          }
        }
      }
    })
  }
}

onMounted(() => {
  initThree()
  animate()
  window.addEventListener('resize', onWindowResize)
})

onBeforeUnmount(() => {
  dispose()
})
</script>

<style scoped>
.piramide-container {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}
</style> 