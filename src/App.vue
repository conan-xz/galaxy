<template>
  <div class="app-container">
    <EarthGlobe 
      :userLocation="userLocation"
      @locationUpdated="updateLocation"
    />
    <UserAvatar 
      :avatarInfo="avatarInfo"
      :horoscopeStatus="horoscopeStatus"
    />
    <HoroscopeInfo 
      :horoscope="horoscope"
    />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import EarthGlobe from './components/EarthGlobe.vue'
import UserAvatar from './components/UserAvatar.vue'
import HoroscopeInfo from './components/HoroscopeInfo.vue'

export default {
  name: 'App',
  components: {
    EarthGlobe,
    UserAvatar,
    HoroscopeInfo
  },
  setup() {
    const userLocation = ref({ lat: 0, lng: 0 })
    const avatarInfo = ref({})
    const horoscopeStatus = ref('')
    const horoscope = ref({})

    const updateLocation = (newLocation) => {
      userLocation.value = newLocation
      // 这里应该调用API来获取新位置的运势信息
      fetchHoroscope(newLocation)
    }

    const fetchHoroscope = async (location) => {
      // 模拟API调用
      horoscope.value = {
        sign: 'Scorpio',
        dailyHoroscope: 'Today is your lucky day!',
        status: 'good'
      }
      horoscopeStatus.value = horoscope.value.status
    }

    onMounted(() => {
      // 获取用户位置
      navigator.geolocation.getCurrentPosition((position) => {
        userLocation.value = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        }
        fetchHoroscope(userLocation.value)
      })

      // 模拟获取用户头像信息
      avatarInfo.value = {
        imageUrl: 'src/assets/scorpio.jpeg',
        zodiacSign: 'Scorpio',
        lifeNumber: 7
      }
    })

    return {
      userLocation,
      avatarInfo,
      horoscopeStatus,
      horoscope,
      updateLocation
    }
  }
}
</script>

<style>
.app-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
  background-color: #1a1a2e;
  color: white;
}
</style>