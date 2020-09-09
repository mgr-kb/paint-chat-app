<template>
  <div class="home">
    <DrawCanvas msg="絵を描くよ"/>
  </div>
</template>

<script>
// @ is an alias to /src
import DrawCanvas from '@/components/DrawCanvas.vue'

export default {
  name: 'Home',
  components: {
    DrawCanvas
  }
}
</script>
