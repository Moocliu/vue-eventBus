<template>
  <div class="about">
    <h1>This is an about page</h1>
  <h2>{{num}}</h2>
  </div>
</template>
<script>
import { eventBus } from '../until/eventBus.js'
export default {

  data () {
    return {
      num: 0
    }
  },
  created () {
    eventBus.$on('eventBusName', (val) => {
      console.log(`about ${val}`)
      this.num = val
      console.log(this.num)
    })
  }
}

</script>
