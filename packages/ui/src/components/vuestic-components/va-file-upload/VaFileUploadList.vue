<template>
  <div
    class="va-file-upload-list"
    :class="`va-file-upload-list--${type}`"
  >
    <template v-if="type === 'list'">
      <va-file-upload-list-item
        v-for="(file, index) in filesList"
        :key="file.name"
        :file="file"
        :color="color"
        @remove="$emit('remove', index)"
      />
    </template>
    <template v-if="type === 'gallery'">
      <va-file-upload-gallery-item
        v-for="(file, index) in filesList"
        :file="file"
        :key="file.name"
        :color="color"
        @remove="$emit('remove', index)"
      />
    </template>
    <va-file-upload-single-item
      v-if="type === 'single' && filesList.length"
      :file="filesList[filesList.length - 1]"
      @remove="$emit('remove-single')"
    />
  </div>
</template>

<script>
import VaFileUploadListItem from './VaFileUploadListItem'
import VaFileUploadGalleryItem from './VaFileUploadGalleryItem'
import VaFileUploadSingleItem from './VaFileUploadSingleItem'
import { makeContextablePropsMixin } from '../../context-test/context-provide/ContextPlugin'

export default {
  name: 'VaFileUploadList',
  mixins: [
    makeContextablePropsMixin({
      type: {
        type: String,
        default: '',
      },
      files: {
        type: [Object, Array],
        default: null,
      },
      color: {
        type: String,
        default: 'success',
      },
    }),
  ],
  components: {
    VaFileUploadListItem,
    VaFileUploadGalleryItem,
    VaFileUploadSingleItem,
  },
  computed: {
    filesList () {
      return this.files.map(this.convertFile)
    },
  },
  methods: {
    convertFile (file) {
      return {
        name: file.name || file.url,
        size: file.size ? this.formatSize(file.size) : '',
        date: this.formatDate(new Date()),
        image: file,
      }
    },
    formatSize (bytes) {
      if (bytes === 0) return '0 Bytes'
      const k = 1024
      const sizes = ['Bytes', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']
      const i = Math.floor(Math.log(bytes) / Math.log(k))
      return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i]
    },
    formatDate (date) {
      if (!date) return ''
      return date.toLocaleDateString('en-US', {
        hour: '2-digit',
        minute: '2-digit',
        month: 'short',
        day: 'numeric',
        year: 'numeric',
      })
    },
  },
}
</script>

<style lang='scss'>
.va-file-upload-list {
  display: flex;
  flex-wrap: wrap;
  padding: 0 0 1.5rem;

  &--gallery {
    padding-bottom: 0.5rem;
  }
}
</style>
