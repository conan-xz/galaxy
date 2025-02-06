<template>
    <div class="avatar-container">
      <img :src="avatarInfo.imageUrl" alt="User Avatar" class="avatar-image">
      <div class="avatar-effects" :class="{ 'good-luck': horoscopeStatus === 'good', 'bad-luck': horoscopeStatus === 'bad' }">
        <div v-if="horoscopeStatus === 'good'" class="sparkle-effect"></div>
        <div v-if="horoscopeStatus === 'bad'" class="umbrella-effect"></div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'UserAvatar',
    props: {
      avatarInfo: Object,
      horoscopeStatus: String
    }
  }
  </script>
  
  <style scoped>
  .avatar-container {
    position: relative;
    width: 100px;
    height: 100px;
  }
  
  .avatar-image {
    width: 100%;
    height: 100%;
    border-radius: 50%;
  }
  
  .avatar-effects {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  
  .sparkle-effect {
    /* 添加闪烁效果的 CSS */
  }
  
  .umbrella-effect {
    /* 添加撑伞效果的 CSS */
  }
  </style>