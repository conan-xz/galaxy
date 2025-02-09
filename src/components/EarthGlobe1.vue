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
        // 场景初始化
        scene = new THREE.Scene()
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
        renderer = new THREE.WebGLRenderer({ antialias: true })
        renderer.setSize(window.innerWidth * 0.6, window.innerHeight * 0.6)
        renderer.setPixelRatio(window.devicePixelRatio)
        document.getElementById('earth-container').appendChild(renderer.domElement)
  
        // 使用GitHub上的行星纹理
        const textureLoader = new THREE.TextureLoader()
        const textureURL = 'https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/planets/earth_atmos_2048.jpg'

        // 加载纹理
        const texture = textureLoader.load(
          textureURL,
          () => { /* 加载成功回调 */ },
          undefined, 
          (err) => console.error('纹理加载失败:', err)
        )
        // 云层纹理
        const cloudsTexture = textureLoader.load(
            'https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/planets/earth_clouds_1024.png'
        )

        // 法线贴图
        const normalTexture = textureLoader.load(
            'https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/planets/earth_normal_2048.jpg'
        )
        
        // 创建地球
        const geometry = new THREE.SphereGeometry(5, 32, 32)
        const material = new THREE.MeshPhongMaterial({
          map: texture,
          bumpScale: 0.05,
          specular: new THREE.Color('grey'),
          shininess: 5
        })
        
        earth = new THREE.Mesh(geometry, material)
        scene.add(earth)
  
        // 添加环境光
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
        scene.add(ambientLight)
  
        // 添加点光源
        const pointLight = new THREE.PointLight(0xffffff, 1.5)
        pointLight.position.set(5, 3, 5)
        scene.add(pointLight)
  
        // 相机位置
        camera.position.z = 10
  
        // 轨道控制器
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
        // 经纬度转换逻辑（示例）
        if (props.userLocation) {
          const { latitude, longitude } = props.userLocation
          const phi = (90 - latitude) * (Math.PI / 180)
          const theta = (longitude + 180) * (Math.PI / 180)
          
          const radius = 5.1 // 略大于地球半径
          const marker = new THREE.Mesh(
            new THREE.SphereGeometry(0.1),
            new THREE.MeshBasicMaterial({ color: 0xff0000 })
          )
          
          marker.position.setFromSphericalCoords(radius, phi, theta)
          scene.add(marker)
        }
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
  