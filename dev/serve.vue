<script>
import { defineComponent } from 'vue';
// Uncomment import and local "components" registration if library is not registered globally.
import { LoadMoreList } from '@/entry.esm';

export default defineComponent({
  name: 'ServeDev',
  components: {
    LoadMoreList,
  }
});
</script>

<template>
  <div id="app">
    <load-more-list/>
  </div>
</template>
