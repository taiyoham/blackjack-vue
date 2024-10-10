<script>
import Home from './Home.vue'
import Play from './Play.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/play': Play
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },
  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },
  mounted() {
    window.addEventListener('hashchange', () => {
		  this.currentPath = window.location.hash
		})
  },
}
</script>

<template>
  <component :is="currentView" />
</template>