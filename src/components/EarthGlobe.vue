<template>
    <div id="earth-container"></div>
  </template>
  
  <script>
  import { onMounted, watch } from 'vue'
  import * as THREE from 'three'
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
  
  export default {
    name: 'EarthGlobe',
    props: {
      userLocation: Object
    },
    setup(props, { emit }) {
      let scene, camera, renderer, earth, controls
  
      const initEarth = () => {
        scene = new THREE.Scene()
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
        renderer = new THREE.WebGLRenderer()
        renderer.setSize(window.innerWidth * 0.6, window.innerHeight * 0.6)
        document.getElementById('earth-container').appendChild(renderer.domElement)
  
        const geometry = new THREE.SphereGeometry(5, 32, 32)
        const texture = new THREE.TextureLoader().load('path/to/earth-texture.jpg')
        const material = new THREE.MeshBasicMaterial({ map: texture })
        earth = new THREE.Mesh(geometry, material)
        scene.add(earth)
  
        camera.position.z = 10
  
        controls = new OrbitControls(camera, renderer.domElement)
        controls.enableDamping = true
        controls.dampingFactor = 0.25
        controls.enableZoom = true
  
        animate()
      }
  
      const animate = () => {
        requestAnimationFrame(animate)
        earth.rotation.y += 0.005
        controls.update()
        renderer.render(scene, camera)
      }
  
      const updateUserLocation = () => {
        // 更新地球仪上用户位置的标记
        // 这里需要根据经纬度计算球面坐标
      }
  
      onMounted(() => {
        initEarth()
      })
  
      watch(() => props.userLocation, () => {
        updateUserLocation()
      })
  
      return {}
    }
  }
  </script>
  
  <style scoped>
  #earth-container {
    width: 60vw;
    height: 60vh;
  }
  </style>