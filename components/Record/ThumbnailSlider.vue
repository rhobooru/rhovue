<template>
  <v-slide-group
    show-arrows
    class="thumbnail-slide-group"
  >
    <v-slide-item
      v-for="record in records"
      :key="record.id"
    >
      <thumbnail
        :record="record"
      />
    </v-slide-item>
  </v-slide-group>
</template>

<script>
import Thumbnail from '~/components/Record/Thumbnail.vue'

export default {
  name: 'ThumbnailSlider',

  components: {
    Thumbnail,
  },

  props: [
    'records',
  ],

  data: () => ({

  }),

  computed: {

  },

  watch: {

  },

  methods: {

  },
}
</script>

<style scoped>

</style>
