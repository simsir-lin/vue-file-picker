<!--
/**
 * @props
 * accept 原生属性
 * length 选择数量
 * maxSize 文件最大尺寸
 *
 * @event
 * @confirm  上传文件已选择
 * @error   错误信息
 */
-->

<template>
  <div class="wrap">
    <div class="file-input-label" @click="startChoose"><slot></slot></div>
    <input
      v-if="length > 1"
      :id="fileInputId"
      class="file-input"
      ref="input"
      type="file"
      multiple="multiple"
      :accept="accept"
      @change="fileChange"
    />
    <input v-else :id="fileInputId" class="file-input" ref="input" type="file" :accept="accept" @change="fileChange" />
  </div>
</template>

<script>
export default {
  props: {
    accept: {
      type: String,
      default: 'image/jpg, image/jpeg, image/png, image/gif',
    },
    length: {
      type: Number,
      default: 1,
    },
    maxSize: {
      type: Number,
      default: 2 * 1024 * 1024,
    },
  },
  data() {
    return {
      files: [],
      fileInputId: '',
    }
  },
  created() {
    this.fileInputId = (this.length <= 1 ? 'fileInput' : 'file-input-multiple') + `-${this._uid}`
  },
  watch: {
    length: function(newValue) {
      this.fileInputId = (newValue <= 1 ? 'fileInput' : 'file-input-multiple') + `-${this._uid}`
    },
  },
  methods: {
    startChoose() {
      if (this.length <= 0) {
        return
      }
      this.$refs.input.value = null
      this.$refs.input.click()
    },
    fileChange() {
      if (this.$refs.input.files.length > this.length) {
        this._error(`最多只可选择${this.length}个`)
        return
      }

      for (var i = 0; i < this.$refs.input.files.length; i++) {
        if (this.$refs.input.files[i].size > this.maxSize) {
          this._error(`文件大小不可超过${(this.maxSize / 1024 / 1024).toFixed(1)}m`)
          return
        }
        this.files.push(this.$refs.input.files[i])
      }
      this._confirm(this.files)
    },
    _confirm(res) {
      this.$emit('confirm', this.length <= 1 ? res[0] : res)
      this.$nextTick(() => {
        this.files = []
      })
    },
    _error(msg) {
      this.$emit('error', {
        msg
      })
    },
  },
}
</script>

<style scoped lang="scss">
.wrap {
  position: relative;
}
.file-input {
  display: none;
}
.file-input-label {
  display: block;
  &:hover {
    cursor: pointer;
  }
}
</style>
