<template>
  <div
    class="thumbnail"
    :style="{ width: thumbnailSize + 'px', height: thumbnailSize + 'px' }"
  >
    <nuxt-link :to="url">
      <v-img
        :lazy-src="imagePlaceholder"
        :src="thumbnail"
        :max-width="thumbnailSize"
        :max-height="thumbnailSize"
        contain
        position="center center"
        eager
        @load="thumbnailLoaded"
      />
    </nuxt-link>
  </div>
</template>

<script>
import imagePlaceholder from '~/assets/js/imagePlaceholder.js'

export default {
  name: 'Thumbnail',

  props: [
    'size',
    'record',
  ],

  data () {
    return {
      imagePlaceholder: imagePlaceholder.src,
      thumbnailSize: 200,
    }
  },

  computed: {
    thumbnail () {
      return this.record.thumbnail
    },

    url () {
      return '/records/' + this.record.id
    },

    aspectRatio () {
      return this.record.width / this.record.height
    },

    imageDimensions () {
      let targetWidth
      let targetHeight

      targetWidth = targetHeight = Math.min(this.thumbnailSize, Math.max(this.record.width, this.record.height))

      if (this.aspectRatio < 1) {
        targetWidth = targetHeight * this.aspectRatio
      } else {
        targetHeight = targetWidth / this.aspectRatio
      }

      return {
        width: targetWidth,
        height: targetHeight
      }
    },
  },

  created () {
    this.thumbnailSize = this.size || this.thumbnailSize
  },

  methods: {
    thumbnailLoaded () {
      this.$emit('loaded')
    },
  },
}
</script>

<style scoped>
.thumbnail {
  display:inline-block;
  position: relative;
  margin:10px;
  vertical-align: top;
}

.thumbnail > a {
  display: block;
  margin-top: 50%;
  transform: translateY(-50%);
  position: relative;
}

.thumbnail .v-image {
  position: relative;
  margin:0 auto;
}
</style>
