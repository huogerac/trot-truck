<script>
import { Geolocation } from '@capacitor/geolocation'
import { ForegroundService } from '@ionic-native/foreground-service'

export default {
  name: 'MyComponent',
  mounted() {
    // Inicialize o serviço em um local apropriado, como no evento 'mounted' do Vue
    this.initForegroundService()
  },
  methods: {
    async initForegroundService() {
      try {
        await ForegroundService.start('Trot Truck', 'Rastreio')

        await ForegroundService.updateNotification({
          title: 'Rastreando seu caminhão',
          text: 'Olhe pra estrada amigo, seu rastreio está funcionando.',
          icon: 'icon_name'
        })

        // Lógica adicional do serviço em primeiro plano
      } catch (error) {
        console.error('Erro ao iniciar o serviço em primeiro plano:', error)
      }
    },
    async stopPosition() {
      await ForegroundService.stop()
    },
    async getLocation() {
      const coordinates = await Geolocation.getCurrentPosition()
      this.state.latitude = coordinates.coords.latitude
      this.state.longitude = coordinates.coords.longitude
      this.positions.push({
        latitude: coordinates.coords.latitude,
        longitude: coordinates.coords.longitude
      })
    },
    segundosEmForeground() {
      setInterval(() => {
        this.counter += 1
        this.getLocation()
      }, 20000)
    }
  },
  data() {
    return {
      state: {
        latitude: '',
        longitude: ''
      },
      counter: 0,
      positions: []
    }
  }
}
</script>

<template>
  <header>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-feature android:name="android.hardware.location.gps" />

    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
      <nav>
        <button @click="getLocation()">Get my Lat & Long</button>
        <button @click="stopPosition()">Stop</button>
        <button @click="segundosEmForeground()">Iniciar rastreio</button>
      </nav>
      <p v-if="state.latitude">LAT {{ state.latitude }}</p>
      <p v-if="state.longitude">LONG {{ state.longitude }}</p>
      <p>pontos salvos {{ counter }}s</p>
      <div>
        <p v-for="(pos, index) in positions" :key="index">
          {{ index }}: {{ pos.latitude }}, {{ pos.longitude }}
        </p>
      </div>
    </div>
  </header>

  <RouterView />
</template>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
