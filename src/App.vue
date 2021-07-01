<template>
  <div class="tw-flex tw-flex-col tw-h-screen">
    <div class="tw-h-12 tw-bg-sky-300 tw-flex-shrink-0 tw-flex tw-items-center tw-p-4">
      <button @click="reset" class="tw-p-1 tw-rounded tw-font-light tw-bg-purple-100">RESET</button>
    </div>
    <LoadMoreList :skeleton-count="0" class="tw-flex-grow" :has-more="hasMore" :loading="loading" @load-more="onLoadMore">
      <VeilScaleTransition group>
        <template v-for="(item,index) of data" :key="index">
          <div class="tw-flex tw-flex-col tw-text-left tw-m-2 tw-font-light tw-p-4 tw-shadow-lg">
            <q>{{ item.text }}</q>
            <blockquote class="tw-self-end tw-text-sm tw-font-normal">
              {{ item.author }}
            </blockquote>
          </div>
        </template>
      </VeilScaleTransition>
    </LoadMoreList>
    <div class="tw-h-12 tw-bg-sky-300 tw-flex-shrink-0 tw-flex tw-items-center tw-p-4">
    </div>
  </div>
</template>

<script>
import LoadMoreList from './components/LoadMoreList.vue'
import { VeilScaleTransition } from 'veil-transitions'

export default {
  components: { LoadMoreList, VeilScaleTransition },
  data() {
    return {
      data: [],
      hasMore: true,
      loading: false,
    }
  },
  methods: {
    async onLoadMore() {
      if (!this.hasMore) {
        return
      }
      this.loading = true
      await new Promise((resolve => setTimeout(resolve, 2000)))
      this.data.push(
          ...Array.from({ length: 10 }).map((it, index) => ({
            index: index,
            text: 'Example moves the world more than doctrine',
            author: 'Henry Miller',
          })),
      )
      if (this.data.length >= 40) {
        this.hasMore = false
      }
      this.loading = false
    },
    reset() {
      this.data = []
      this.hasMore = true
    },
  },
}
</script>

<style>
</style>
