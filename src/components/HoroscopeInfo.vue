<template>
    <div class="horoscope-container">
      <h2>{{ horoscope.sign }}</h2>
      <p>{{ horoscope.dailyHoroscope }}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: 'HoroscopeInfo',
    props: {
      horoscope: Object
    }
  }
  </script>
  
  <style scoped>
  .horoscope-container {
    text-align: center;
    padding: 20px;
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 10px;
    margin-top: 20px;
  }
  </style>