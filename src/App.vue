<template>
  <popup
  @close="closePopup"
   v-show="showPopup"></popup>
  <button 
  @click="openPopup"
  class="open-popup-btn">
    Налоговый вычет
    </button>
</template>

<script>
import { ref } from 'vue';
import Popup from './components/Popup.vue';
export default {
  name: 'App',
  components: {
    Popup,
  },
  setup() {

    const showPopup = ref(false);

    function openPopup() {
      showPopup.value = true;
    }

    function closePopup() {
      showPopup.value = false;
    }


    return {
      openPopup,
      showPopup,
      closePopup
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

button, input {
  font-family: 'Roboto', sans-serif;
}

input {
  font-size: .875rem;
}

button {
  cursor: pointer;
}

body {
    background-color: #EA0029;
    background: linear-gradient(255.35deg, #DC3131 0.83%, rgba(255, 79, 79, 0) 108.93%), #FF5E56;
}

#app {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.open-popup-btn {
  border: 1px solid #fff;
  background: transparent;
  border-radius: 6px;
  padding: 16px 32px;
  color: #fff;
  cursor: pointer;
  font-weight: 500;
  &:hover {
    background: #fff;
    color: #000000;
  }
}

.btn-bg {
  background-color: #EA0029;
  background: linear-gradient(255.35deg, #DC3131 0.83%, rgba(255, 79, 79, 0) 108.93%), #FF5E56;
  &:hover {
    background: #EA0029;
  }
}

</style>
