<template>
  <div
      class="tw-overflow-y-auto tw-overflow-x-hidden"
      @scroll="onScroll"
  >
    <slot></slot>
    <slot name="skeleton" v-bind:loading="loading">
      <template v-if="loading">
        <template v-for="index in skeletonCount" :key="index">
          <slot name="skeleton-item">
            <div class="tw-flex tw-animate-pulse tw-h-16 tw-mx-5 tw-my-6">
              <div class="tw-bg-gray-200 tw-h-16 tw-w-16 tw-flex-shrink-0 tw-mx-3">
              </div>
              <div class="tw-flex-grow tw-justify-between tw-flex tw-flex-col tw-mx-3">
                <div class="tw-bg-gray-200 tw-h-1/4"></div>
                <div class="tw-bg-gray-200 tw-h-1/4"></div>
                <div class="tw-bg-gray-200 tw-h-1/4"></div>
              </div>
            </div>
          </slot>
        </template>
      </template>
    </slot>
    <slot v-bind:hasMore="hasMore" v-bind:loading="loading" name="footer">
      <slot name="loading" v-bind:hasMore="hasMore" v-bind:loading="loading">
        <div class="tw-cursor-wait tw-p-2 tw-text-lg tw-text-gray-400 tw-flex tw-items-center tw-justify-center tw-text-center"
             v-if="loading">
          <div class="tw-animate-spin tw-p-2">
            <svg class="icon" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"
                 width="28" height="28">
              <path
                  fill="#888"
                  d="M988 548c-19.9 0-36-16.1-36-36 0-59.4-11.6-117-34.6-171.3a440.45 440.45 0 0 0-94.3-139.9 437.71 437.71 0 0 0-139.9-94.3C629 83.6 571.4 72 512 72c-19.9 0-36-16.1-36-36s16.1-36 36-36c69.1 0 136.2 13.5 199.3 40.3C772.3 66 827 103 874 150c47 47 83.9 101.8 109.7 162.7 26.7 63.1 40.2 130.2 40.2 199.3 0.1 19.9-16 36-35.9 36z"/>
            </svg>
          </div>
        </div>
      </slot>
      <slot name="no-more" v-bind:hasMore="hasMore" v-bind:loading="loading">
        <div class="tw-text-sm tw-p-2 tw-text-gray-400 tw-text-center" v-if="!loading && !hasMore">
          {{ noMoreText || '没有更多了' }}
        </div>
      </slot>
      <slot name="load-more">
        <div
            class="tw-text-sm tw-p-2 tw-cursor-pointer tw-flex tw-justify-center tw-items-center tw-text-gray-400 tw-text-center"
            @click="clickLoadMore"
            v-if="!loading && hasMore"
        >
          {{ loadMoreText || '加载更多' }}
          <div class="tw-p-2 tw-animate-pulse">
            <svg class="icon" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"
                 width="18" height="18">
              <path
                  d="M533.712538 101.851631c13.248955-7.183654 30.638839-1.148579 38.658739 13.420235l73.095947 132.862338c8.060201 14.558738 3.737918 32.351632-9.511037 39.535285s-30.648914 1.148579-38.658739-13.420234l-73.106023-132.862338c-8.009825-14.558738-3.727843-32.351632 9.521113-39.535286z"
                  fill="#555" />
              <path
                  d="M700.347117 10.388501c-8.956898-12.090301-27.26363-13.883696-40.673789-4.030101L536.815715 96.350544c-13.410159 9.82337-17.047325 27.727091-8.060201 39.787167s27.273705 13.883696 40.683864 4.030101L692.266766 50.175668c13.410159-9.82337 17.047325-27.727091 8.080351-39.787167zM485.804718 922.148359c-13.248955 7.183654-30.648914 1.148579-38.658739-13.420235l-73.106022-132.862338c-8.009825-14.558738-3.727843-32.351632 9.521112-39.535285s30.648914-1.148579 38.658739 13.420234l73.106023 132.862338c8.009825 14.558738 3.727843 32.351632-9.521113 39.535286z"
                  fill="#555"/>
              <path
                  d="M319.160063 1013.611489c8.966974 12.090301 27.273705 13.883696 40.683865 4.030101l122.857613-89.982069c13.410159-9.82337 17.047325-27.727091 8.060201-39.787167s-27.273705-13.883696-40.683865-4.0301L327.25049 973.824322c-13.410159 9.82337-17.017099 27.727091-8.090427 39.787167z"
                  fill="#555"/>
              <path
                  d="M462.470436 928.485692a32.381857 32.381857 0 0 1-4.0301-0.251882A424.168076 424.168076 0 0 1 195.063194 790.112192a418.878569 418.878569 0 0 1-39.404307-507.520629 423.744915 423.744915 0 0 1 174.825759-152.488927 32.240804 32.240804 0 0 1 27.49536 58.325629 359.333834 359.333834 0 0 0-148.247246 129.305774 354.366735 354.366735 0 0 0 33.389382 429.387056A359.756995 359.756995 0 0 0 466.399784 864.255965a32.240804 32.240804 0 0 1-3.969649 64.229727zM660.852133 902.511694a32.240804 32.240804 0 0 1-12.191054-62.103848 358.678943 358.678943 0 0 0 160.408074-129.164721 354.376811 354.376811 0 0 0-31.29373-440.590735 359.36406 359.36406 0 0 0-229.927308-114.777262 32.243826 32.243826 0 0 1 6.297032-64.17935 423.785216 423.785216 0 0 1 271.135085 135.371075 418.868494 418.868494 0 0 1 36.915721 520.719208A423.029572 423.029572 0 0 1 673.033111 900.103709a32.109826 32.109826 0 0 1-12.180978 2.407985z"
                  fill="#555"/>
            </svg>
          </div>
        </div>
      </slot>
      <slot name="footer-append"></slot>
    </slot>
  </div>
</template>

<script>
import './index.css'
import { onMounted, ref, toRefs } from 'vue'

export default {
  name: 'LoadMoreList',
  emits: ['load-more'],
  props: {
    //是否有更多列表项
    hasMore: {
      type: Boolean,
      default: true,
    },
    //加载中
    loading: {
      type: Boolean,
      default: false,
    },
    //加载中
    loadOnMounted: {
      type: Boolean,
      default: true,
    },
    skeletonCount: {
      type: Number,
      default: 3,
    },
    bottomThreshold: {
      type: Number,
      default: 100,
    },
    noMoreText: String,
    loadMoreText: String,
  },
  data: function () {
    return {
      bottom: false,
    }
  },
  setup(props,{emit}){
    let { bottomThreshold, loading,loadOnMounted } = toRefs(props);
    let bottom = false;
    let firstLoading = ref(true);
    const onScroll = (e)=>{
      let top = e.target.scrollTop
      let height = e.target.scrollHeight
      let visible = e.target.clientHeight
      bottom = height <= top + visible + bottomThreshold.value
      if (bottom && !loading.value) {
        emit('load-more')
        firstLoading.value = false
      }
    }

    onMounted(()=>{
      if(loadOnMounted.value){
        emit('load-more')
      }
    })

    const clickLoadMore = ()=>{
      emit('load-more')
    }

    return {
      onScroll,
      clickLoadMore,
      firstLoading
    }
  },
}
</script>
