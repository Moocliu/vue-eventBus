<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
   <button @click="handleAdd">随机增加</button>
   <h1>{{ num }}</h1>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import { eventBus } from '../until/eventBus.js'
export default {

  name: 'home',
  components: {
    // HelloWorld
  },
  data () {
    return {
      num: 0
    }
  },
  methods: {
    handleAdd: function () {
      this.num = Math.floor(Math.random() * 100 + 1)
    }
  },
  destroyed () {
    eventBus.$emit('eventBusName', this.num)
  }

}
</script>
