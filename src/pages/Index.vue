<template>
<div>
  <Header />
  <v-container grid-list-md fluid>
      <!-- Learn how to use images here: https://gridsome.org/docs/images -->
      <!-- <g-image alt="Example image" src="~/favicon.png" width="135" />
      
      <h1>Hello, world!</h1>
    
      <p>
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Pariatur excepturi labore tempore expedita, et iste tenetur suscipit explicabo! Dolores, aperiam non officia eos quod asperiores
      </p>

      <p class="home-links">
        <a href="https://gridsome.org/docs" target="_blank" rel="noopener">Gridsome Docs</a>
        <a href="https://github.com/gridsome/gridsome" target="_blank" rel="noopener">GitHub</a>
      </p> -->
      <GameOfLife />  
  </v-container>
  <Footer />
</div>
</template>

<script>
import GameOfLife from '@/components/GameOfLife.vue';
import Header from '@/components/Header.vue';
import Footer from '@/components/Footer.vue';

import 'vuetify/dist/vuetify.min.css'
import Vuetify from 'vuetify';


export default {
  components: {
    GameOfLife,
    Header,
    Footer
  },
  data() {
    return {

    }
  },
  methods: {

  }
}
</script>

<style>
.home-links a {
  margin-right: 1rem;
}
</style>
