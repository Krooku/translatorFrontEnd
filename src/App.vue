<script setup>
import { ref, onMounted } from 'vue'
import gsap from 'gsap'

const isMenuOpen = ref(false)
const selection = ref(0)

const selectedOption = ref('EN-US') // Przechowuje wybraną opcję

const options = [
  { value: 'PL', label: 'PL' },
  { value: 'EN-US', label: 'EN-US' },
  { value: 'EN-GB', label: 'EN-GB' },
  { value: 'DE', label: 'DE' },
  { value: 'DA', label: 'DA' },
]

// Funkcja do obsługi kliknięcia
function showMenu() {
  isMenuOpen.value = !isMenuOpen.value
  gsap.to('.content_wrapper', {
    x: isMenuOpen.value ? 0 : '101%',
    duration: 0.5,
    ease: 'power2.out',
  })
}

function setSelection(number) {
  // sendDataToPython()
  gsap.to('.content_wrapper', {
    x: '300px',
    duration: 0.5,
    ease: 'power2.out',
    onComplete: () => {
      selection.value = number
      gsap.to('.content_wrapper', {
        x: 0,
        duration: 0.5,
        ease: 'power2.out',
      })
    },
  })
}

const sendDataToPython = () => {
  window.electron.ipcRenderer.send('send-data', { lang: selectedOption.value })
}

// Nasłuchuj na wiadomości z backendu Pythona
onMounted(() => {
  console.log('onMounted')
  window.electron.ipcRenderer.on('python-data', (data) => {
    console.log('Dane z Pythona:', data)
  })
})
</script>

<template>
  <div class="container">
    <div class="nav_wrapper">
      <div class="nav">
        <select id="languages" v-model="selectedOption" @change="sendDataToPython">
          <option v-for="option in options" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>
        <div class="menu" @click="showMenu">
          <div class="wrapper" :class="{ active: isMenuOpen }">
            <div class="hamburger">
              <span class="b b1"></span>
              <span class="b b2"></span>
              <span class="b b3"></span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="menu_content_wrapper">
      <div class="content_wrapper">
        <div class="menu_content">
          <div @click="setSelection(0)" class="item">Konto</div>
          <div @click="setSelection(1)" class="item">Opcje języka</div>
          <div @click="setSelection(2)" class="item">Skróty klawiszowe</div>
          <div @click="setSelection(3)" class="item">Zgłoś błąd</div>
        </div>
        <div class="item_content">
          <div class="test" v-if="selection == 0">WORK IN PROGRESS</div>
          <div class="test" v-else-if="selection == 1">WORK IN PROGRESS</div>
          <div class="test" v-else-if="selection == 2">WORK IN PROGRESS</div>
          <div class="test" v-else-if="selection == 3">WORK IN PROGRESS</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.container {
  position: relative;
  background-color: transparent;
  z-index: 0;
  min-width: 100px;
  border-radius: 12px;
  overflow: hidden;
}

.nav_wrapper {
  background-color: transparent;
  position: relative;
  width: 100%;
  height: auto;
  display: flex;
  justify-content: flex-end;
}

.menu-icon {
  padding: 10px 30px;
}

.menu-icon:hover {
  cursor: pointer;
}

.menu {
  width: 35px;
  height: 35px;
  margin-left: 20px;
  background-color: transparent;
  z-index: 40;
  display: flex;
  align-items: center;
  justify-content: center;
  .wrapper {
    position: relative;
    width: 52px;
    height: 52px;
    background-color: transparent;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    .hamburger {
      position: relative;
      width: 25px;
      height: 19px;
      background-color: transparent;
      overflow: hidden;
      transition: transform 0.5s ease-in-out;

      .b {
        position: absolute;
        transition: transform 0.2s ease-in-out;
        background-color: black;
        width: 100%;
        height: 2px;
      }

      .b1 {
        top: 0;
        left: 0;
        width: 100%;
        height: 2px;
      }

      .b2 {
        top: calc(50% - 1px);
        transform: translateX(-30%);
        left: 0;
      }
      .b3 {
        bottom: 0;
        left: 0;
      }
    }
  }

  .active {
    // background-color: red;
  }

  .active .hamburger .b2 {
    transform: translateX(0) skewY(-45deg);
    transition: transform 0.1s ease;
    height: 3px;
  }
  .active .hamburger .b1 {
    transform: translateX(1px) translateY(9px) skewY(45deg);
    transition: transform 0.1s ease;
    height: 3px;
  }

  .active .hamburger .b3 {
    transform: translateY(20px);
    transition: transform 0.1s ease;
  }
}

.menu:hover .wrapper:not(.active) .hamburger .b2 {
  transform: translateX(0);
}

.menu:hover {
  cursor: pointer;
}

.nav {
  background-color: lightblue;
  border-radius: 12px;

  min-width: 150px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 36px;
  transform: translateX(20px);
}
.menu_content_wrapper {
  background-color: transparent;
  position: relative;
  width: 100%;
  display: flex;
  justify-content: flex-end;
  overflow: hidden;
  margin-top: 5px;
  border-radius: 12px;
}

.content_wrapper {
  background-color: transparent;
  transform: translateX(101%);
  /* transition: transform 0.3s ease-in-out; */
  display: flex;
  border-radius: 12px;
}

.menu_content {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: lightblue;
  border-top-left-radius: 12px;
  border-bottom-left-radius: 12px;
  padding: 30px;
  /* Zapobieganie rozciąganiu kontenera na całą szerokość */
}
.content_wrapper.menu-open {
  transform: translateX(0);
}

.item_content {
  width: 300px;
  background-color: lightblue;
}

.item {
  background-color: lightblue;
  font-weight: bold;
  transition: color 0.3s ease-in-out;

  &:hover {
    cursor: pointer;
    color: rgb(240, 72, 20);
  }
}

.test {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}

select {
  padding: 8px;
  border-radius: 4px;
  border: 1px solid #ccc;
  background-color: lightblue; /* Domyślny kolor tła */
  color: #333;
  font-size: 16px;
  cursor: pointer;
  outline: none;
  transition: background-color 0.3s ease;
}

/* Zmiana koloru tła po najechaniu */
select:hover {
  background-color: deepskyblue;
}

/* Zmiana koloru tła po kliknięciu (aktywacja) */
select:focus {
  background-color: dodgerblue;
  border-color: #007bff;
}

/* Opcjonalnie: zmiana tła dla opcji */
option {
  background-color: lightblue;
  color: black;
}
</style>
