<template>
  <router-view />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import useDarkmode from './use/Darkmode'

export default defineComponent({
  name: 'App',

  setup() {
    useDarkmode()
  },
})
</script>
