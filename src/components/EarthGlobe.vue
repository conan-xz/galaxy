<template>
    <div id="earth-container"></div>
  </template>
  
  <script>
  import { onMounted, watch } from 'vue'
  import * as THREE from 'three'
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
  
  export default {
    name: 'EnhancedEarth',
    props: {
      userLocation: Object
    },
    setup(props) {
      console.log('EnhancedEarth component setup initialized') // Debugging line
  
      let scene, camera, renderer, earth, clouds, controls
      const CLOUD_ROTATION_OFFSET = 0.0005 // 云层旋转速度差异
  
      const initTextures = async () => {
        const textureLoader = new THREE.TextureLoader()
        
        try {  
          // Load textures in parallel
          const [earthTexture, normalTexture, cloudsTexture] = await Promise.all([
            textureLoader.loadAsync('src/assets/earth_atmos_2048.jpeg'),
            textureLoader.loadAsync('src/assets/earth_normal_2048.jpg'),
            textureLoader.loadAsync('src/assets/earth_clouds_1024.png')
          ])
    
          return { earthTexture, normalTexture, cloudsTexture }
        } catch (error) {
          console.error('Error loading textures:', error) // Error handling
        }
      }
  
      const createEarth = (textures) => {
        if (!textures) {
          console.error('Textures not available, skipping Earth creation')
          return
        }
  
        // Earth material with normal map
        const earthMaterial = new THREE.MeshPhongMaterial({
          map: textures.earthTexture,
          normalMap: textures.normalTexture,
          normalScale: new THREE.Vector2(0.8, 0.8),
          specular: new THREE.Color(0x444444),
          shininess: 50,
          bumpScale: 0.05
        })
  
        // Earth mesh
        const geometry = new THREE.SphereGeometry(5, 64, 64)
        earth = new THREE.Mesh(geometry, earthMaterial)
        scene.add(earth)
  
        // Cloud material (transparent)
        const cloudsMaterial = new THREE.MeshPhongMaterial({
          map: textures.cloudsTexture,
          transparent: true,
          opacity: 0.8,
          depthWrite: false
        })
  
        // Cloud mesh (slightly larger sphere)
        const cloudsGeometry = new THREE.SphereGeometry(5.02, 64, 64)
        clouds = new THREE.Mesh(cloudsGeometry, cloudsMaterial)
        scene.add(clouds)
  
        console.log('Clouds added to scene') // Debugging line
      }
  
      const initScene = async () => {  
        scene = new THREE.Scene()
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
        renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
        renderer.setSize(window.innerWidth * 0.6, window.innerHeight * 0.6)
        document.getElementById('earth-container').appendChild(renderer.domElement)
  
        // Load textures and create Earth
        const textures = await initTextures()
        if (textures) {
          createEarth(textures)
        } else {
          console.error('Failed to load textures, skipping Earth creation')
        }
  
        // Lighting setup
        const ambientLight = new THREE.AmbientLight(0xffffff, 1.0)
        const directionalLight = new THREE.DirectionalLight(0xffffff, 1.5)
        directionalLight.position.set(5, 3, 5)
        scene.add(ambientLight, directionalLight)
  
        // Camera and controls
        camera.position.z = 12
        controls = new OrbitControls(camera, renderer.domElement)
        controls.enableDamping = true
        controls.dampingFactor = 0.05
  
        console.log('Scene initialized, starting animation...') // Debugging line
        animate()
      }
  
      const animate = () => {
        requestAnimationFrame(animate)
        
        // 地球与云层差异旋转
        earth.rotation.y += 0.002
        clouds.rotation.y += 0.002 + CLOUD_ROTATION_OFFSET
        
        controls.update()
        renderer.render(scene, camera)
      }
  
      // 位置标记方法保持不变...
      onMounted(() => {
        initScene()
      })
  
      return {}
    }
  }
  </script>
  
  <style scoped>
  #earth-container {
    width: 60vw;
    height: 60vh;
    background: radial-gradient(circle at center, #131313 100%, #030303 100%);
  }
  </style>
  