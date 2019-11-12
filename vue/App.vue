<template>
  <div id="app">
    <vue-file-picker @confirm="showImage" @error="showMsg">
      <p>这是slot内容，点击即可选文件</p>
    </vue-file-picker>
    <img v-if="image" :src="image" class="image">
  </div>
</template>

<script>
import vueFilePicker from '../src/vue-file-picker'
export default {
  name: 'app',
  components: {
    vueFilePicker
  },
  data() {
    return {
      image: ''
    }
  },
  methods: {
    showMsg(res) {
      alert(res.msg)
    },
    showImage(file) {
      this._getCropedImageURL(file).then(url => {
        this.$set(this, 'image', url)
      })
    },
    _getCropedImageURL(file) {
      return new Promise(resolve => {
        let reader = new FileReader()
        reader.onload = function() {
          resolve(reader.result)
        }
        reader.readAsDataURL(file)
      })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
}
.image {
  width: 300px;
  height: auto;
  margin-top: 20px;
}
</style>
