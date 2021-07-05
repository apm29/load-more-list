<script>
import { defineComponent, onMounted, ref, toRefs } from 'vue'
import { VeilScaleTransition, VeilSlideYTransition } from 'veil-transitions'
import 'veil-transitions/src/components/index.css'

export default /*#__PURE__*/defineComponent({
  name: 'LoadMoreList',
  emits: ['load-more'],
  components: { VeilSlideYTransition, VeilScaleTransition },
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
  setup(props, { emit }) {
    let { bottomThreshold, loading, loadOnMounted } = toRefs(props)
    let bottom = false
    let firstLoading = ref(true)
    const onScroll = (e) => {
      let top = e.target.scrollTop
      let height = e.target.scrollHeight
      let visible = e.target.clientHeight
      bottom = height <= top + visible + bottomThreshold.value
      if (bottom && !loading.value) {
        emit('load-more')
        firstLoading.value = false
      }
    }

    onMounted(() => {
      if (loadOnMounted.value) {
        emit('load-more')
      }
    })

    const clickLoadMore = () => {
      emit('load-more')
    }

    return {
      onScroll,
      clickLoadMore,
      firstLoading,
    }
  },
})
</script>

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
    <slot v-bind:hasMore="hasMore" v-bind:clickLoadMore="clickLoadMore" v-bind:loading="loading" name="footer">
      <slot name="loading" v-bind:hasMore="hasMore" v-bind:loading="loading">
        <VeilScaleTransition>
          <div
              class="tw-cursor-wait tw-p-2 tw-text-lg tw-text-gray-400 tw-flex tw-items-center tw-justify-center tw-text-center"
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
        </VeilScaleTransition>
      </slot>
      <slot name="no-more" v-bind:hasMore="hasMore" v-bind:loading="loading">
        <div class="tw-text-sm tw-p-2 tw-text-gray-400 tw-text-center" v-if="!loading && !hasMore">
          {{ noMoreText || '没有更多了' }}
        </div>
      </slot>
      <slot name="load-more" v-bind:clickLoadMore="clickLoadMore">
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
                  fill="#555"/>
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

<style scoped>
/*! tailwindcss v2.2.4 | MIT License | https://tailwindcss.com *//*! modern-normalize v1.1.0 | MIT License | https://github.com/sindresorhus/modern-normalize */
*, ::after, ::before {
  box-sizing: border-box
}

html {
  -moz-tab-size: 4;
  -o-tab-size: 4;
  tab-size: 4
}

html {
  line-height: 1.15;
  -webkit-text-size-adjust: 100%
}

body {
  margin: 0
}

body {
  font-family: system-ui, -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji'
}

hr {
  height: 0;
  color: inherit
}

abbr[title] {
  -webkit-text-decoration: underline dotted;
  text-decoration: underline dotted
}

b, strong {
  font-weight: bolder
}

code, kbd, pre, samp {
  font-family: ui-monospace, SFMono-Regular, Consolas, 'Liberation Mono', Menlo, monospace;
  font-size: 1em
}

small {
  font-size: 80%
}

sub, sup {
  font-size: 75%;
  line-height: 0;
  position: relative;
  vertical-align: baseline
}

sub {
  bottom: -.25em
}

sup {
  top: -.5em
}

table {
  text-indent: 0;
  border-color: inherit
}

button, input, optgroup, select, textarea {
  font-family: inherit;
  font-size: 100%;
  line-height: 1.15;
  margin: 0
}

button, select {
  text-transform: none
}

[type=button], [type=reset], button {
  -webkit-appearance: button
}

legend {
  padding: 0
}

progress {
  vertical-align: baseline
}

summary {
  display: list-item
}

blockquote, dd, dl, figure, h1, h2, h3, h4, h5, h6, hr, p, pre {
  margin: 0
}

button {
  background-color: transparent;
  background-image: none
}

fieldset {
  margin: 0;
  padding: 0
}

ol, ul {
  list-style: none;
  margin: 0;
  padding: 0
}

html {
  font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  line-height: 1.5
}

body {
  font-family: inherit;
  line-height: inherit
}

*, ::after, ::before {
  box-sizing: border-box;
  border-width: 0;
  border-style: solid;
  border-color: currentColor
}

hr {
  border-top-width: 1px
}

img {
  border-style: solid
}

textarea {
  resize: vertical
}

input::-moz-placeholder, textarea::-moz-placeholder {
  opacity: 1;
  color: #a1a1aa
}

input:-ms-input-placeholder, textarea:-ms-input-placeholder {
  opacity: 1;
  color: #a1a1aa
}

input::placeholder, textarea::placeholder {
  opacity: 1;
  color: #a1a1aa
}

button {
  cursor: pointer
}

table {
  border-collapse: collapse
}

h1, h2, h3, h4, h5, h6 {
  font-size: inherit;
  font-weight: inherit
}

a {
  color: inherit;
  text-decoration: inherit
}

button, input, optgroup, select, textarea {
  padding: 0;
  line-height: inherit;
  color: inherit
}

code, kbd, pre, samp {
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace
}

audio, canvas, embed, iframe, img, object, svg, video {
  display: block;
  vertical-align: middle
}

img, video {
  max-width: 100%;
  height: auto
}

*, ::after, ::before {
  --tw-border-opacity: 1;
  border-color: rgba(228, 228, 231, var(--tw-border-opacity))
}

.tw-m-2 {
  margin: .5rem
}

.tw-mx-3 {
  margin-left: .75rem;
  margin-right: .75rem
}

.tw-mx-5 {
  margin-left: 1.25rem;
  margin-right: 1.25rem
}

.tw-my-6 {
  margin-top: 1.5rem;
  margin-bottom: 1.5rem
}

.tw-flex {
  display: flex
}

.tw-h-12 {
  height: 3rem
}

.tw-h-16 {
  height: 4rem
}

.tw-h-1\/4 {
  height: 25%
}

.tw-h-screen {
  height: 100vh
}

.tw-w-16 {
  width: 4rem
}

.tw-flex-shrink-0 {
  flex-shrink: 0
}

.tw-flex-grow {
  flex-grow: 1
}

@-webkit-keyframes tw-spin {
  to {
    transform: rotate(360deg)
  }
}

@keyframes tw-spin {
  to {
    transform: rotate(360deg)
  }
}

@-webkit-keyframes tw-ping {
  100%, 75% {
    transform: scale(2);
    opacity: 0
  }
}

@keyframes tw-ping {
  100%, 75% {
    transform: scale(2);
    opacity: 0
  }
}

@-webkit-keyframes tw-pulse {
  50% {
    opacity: .5
  }
}

@keyframes tw-pulse {
  50% {
    opacity: .5
  }
}

@-webkit-keyframes tw-bounce {
  0%, 100% {
    transform: translateY(-25%);
    -webkit-animation-timing-function: cubic-bezier(.8, 0, 1, 1);
    animation-timing-function: cubic-bezier(.8, 0, 1, 1)
  }
  50% {
    transform: none;
    -webkit-animation-timing-function: cubic-bezier(0, 0, .2, 1);
    animation-timing-function: cubic-bezier(0, 0, .2, 1)
  }
}

@keyframes tw-bounce {
  0%, 100% {
    transform: translateY(-25%);
    -webkit-animation-timing-function: cubic-bezier(.8, 0, 1, 1);
    animation-timing-function: cubic-bezier(.8, 0, 1, 1)
  }
  50% {
    transform: none;
    -webkit-animation-timing-function: cubic-bezier(0, 0, .2, 1);
    animation-timing-function: cubic-bezier(0, 0, .2, 1)
  }
}

.tw-animate-spin {
  -webkit-animation: tw-spin 1s linear infinite;
  animation: tw-spin 1s linear infinite
}

.tw-animate-pulse {
  -webkit-animation: tw-pulse 2s cubic-bezier(.4, 0, .6, 1) infinite;
  animation: tw-pulse 2s cubic-bezier(.4, 0, .6, 1) infinite
}

.tw-cursor-pointer {
  cursor: pointer
}

.tw-cursor-wait {
  cursor: wait
}

.tw-flex-col {
  flex-direction: column
}

.tw-items-center {
  align-items: center
}

.tw-justify-center {
  justify-content: center
}

.tw-justify-between {
  justify-content: space-between
}

.tw-self-end {
  align-self: flex-end
}

.tw-overflow-y-auto {
  overflow-y: auto
}

.tw-overflow-x-hidden {
  overflow-x: hidden
}

.tw-rounded {
  border-radius: .25rem
}

.tw-bg-gray-200 {
  --tw-bg-opacity: 1;
  background-color: rgba(228, 228, 231, var(--tw-bg-opacity))
}

.tw-bg-purple-100 {
  --tw-bg-opacity: 1;
  background-color: rgba(243, 232, 255, var(--tw-bg-opacity))
}

.tw-bg-sky-300 {
  --tw-bg-opacity: 1;
  background-color: rgba(125, 211, 252, var(--tw-bg-opacity))
}

.tw-p-1 {
  padding: .25rem
}

.tw-p-2 {
  padding: .5rem
}

.tw-p-4 {
  padding: 1rem
}

.tw-text-left {
  text-align: left
}

.tw-text-center {
  text-align: center
}

.tw-text-sm {
  font-size: .875rem;
  line-height: 1.25rem
}

.tw-text-lg {
  font-size: 1.125rem;
  line-height: 1.75rem
}

.tw-font-light {
  font-weight: 300
}

.tw-font-normal {
  font-weight: 400
}

.tw-text-gray-400 {
  --tw-text-opacity: 1;
  color: rgba(161, 161, 170, var(--tw-text-opacity))
}

*, ::after, ::before {
  --tw-shadow: 0 0 #0000
}

.tw-shadow-lg {
  --tw-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
  box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000), var(--tw-ring-shadow, 0 0 #0000), var(--tw-shadow)
}

*, ::after, ::before {
  --tw-ring-inset: var(--tw-empty,); /*!*//*!*/
  --tw-ring-offset-width: 0px;
  --tw-ring-offset-color: #fff;
  --tw-ring-color: rgba(59, 130, 246, 0.5);
  --tw-ring-offset-shadow: 0 0 #0000;
  --tw-ring-shadow: 0 0 #0000
}

.carousel-transition-enter-from {
  transform: translate(100%, 0)
}

.carousel-transition-leave-from, .carousel-transition-leave-to {
  position: absolute;
  top: 0;
  transform: translate(-100%, 0)
}

.carousel-reverse-transition-enter-from {
  transform: translate(-100%, 0)
}

.carousel-reverse-transition-leave-from, .carousel-reverse-transition-leave-to {
  position: absolute;
  top: 0;
  transform: translate(100%, 0)
}

.dialog-transition-enter-active {
  transition: 225ms cubic-bezier(0, 0, .2, 1)
}

.dialog-transition-leave-active {
  transition: 125ms cubic-bezier(.4, 0, 1, 1)
}

.dialog-transition-enter-active, .dialog-transition-leave-active {
  transition-property: transform, opacity
}

.dialog-transition-enter-from, .dialog-transition-leave-to {
  transform: scale(.9);
  opacity: 0
}

.dialog-transition-enter-to, .dialog-transition-leave-from {
  opacity: 1
}

.dialog-bottom-transition-enter-from, .dialog-bottom-transition-leave-to {
  transform: translateY(100%)
}

.dialog-top-transition-enter-from, .dialog-top-transition-leave-to {
  transform: translateY(-100%)
}

.picker-reverse-transition-enter-from, .picker-reverse-transition-leave-to, .picker-transition-enter-from, .picker-transition-leave-to {
  opacity: 0
}

.picker-reverse-transition-leave-active, .picker-reverse-transition-leave-from, .picker-reverse-transition-leave-to, .picker-transition-leave-active, .picker-transition-leave-from, .picker-transition-leave-to {
  position: absolute !important
}

.picker-transition-enter-from {
  transform: translate(0, 100%)
}

.picker-transition-leave-to {
  transform: translate(0, -100%)
}

.picker-reverse-transition-enter-from {
  transform: translate(0, -100%)
}

.picker-reverse-transition-leave-to {
  transform: translate(0, 100%)
}

.picker-title-transition-enter-to, .picker-title-transition-leave-from {
  transform: translate(0, 0)
}

.picker-title-transition-enter-from {
  transform: translate(-100%, 0)
}

.picker-title-transition-leave-to {
  opacity: 0;
  transform: translate(100%, 0)
}

.picker-title-transition-leave-active, .picker-title-transition-leave-from, .picker-title-transition-leave-to {
  position: absolute !important
}

.tab-transition-enter-from {
  transform: translate(100%, 0)
}

.tab-transition-leave-active, .tab-transition-leave-from {
  position: absolute;
  top: 0
}

.tab-transition-leave-to {
  position: absolute;
  transform: translate(-100%, 0)
}

.tab-reverse-transition-enter-from {
  transform: translate(-100%, 0)
}

.tab-reverse-transition-leave-from, .tab-reverse-transition-leave-to {
  top: 0;
  position: absolute;
  transform: translate(100%, 0)
}

.expand-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.expand-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.expand-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.expand-x-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.expand-x-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.expand-x-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scale-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scale-transition-enter-from, .scale-transition-leave-from, .scale-transition-leave-to {
  opacity: 0;
  transform: scale(0)
}

.scale-rotate-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-transition-enter-from, .scale-rotate-transition-leave, .scale-rotate-transition-leave-to {
  opacity: 0;
  transform: scale(0) rotate(-45deg)
}

.scale-rotate-reverse-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-reverse-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-reverse-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scale-rotate-reverse-transition-enter-from, .scale-rotate-reverse-transition-leave-from, .scale-rotate-reverse-transition-leave-to {
  opacity: 0;
  transform: scale(0) rotate(45deg)
}

.message-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.message-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.message-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.message-transition-enter-from, .message-transition-leave-to {
  opacity: 0;
  transform: translateY(-15px)
}

.message-transition-leave-active, .message-transition-leave-from {
  position: absolute
}

.slide-y-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-transition-enter-from, .slide-y-transition-leave-to {
  opacity: 0;
  transform: translateY(-15px)
}

.slide-y-reverse-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-reverse-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-reverse-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.slide-y-reverse-transition-enter-from, .slide-y-reverse-transition-leave-to {
  opacity: 0;
  transform: translateY(15px)
}

.scroll-y-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-transition-enter-from, .scroll-y-transition-leave-to {
  opacity: 0
}

.scroll-y-transition-enter-from {
  transform: translateY(-15px)
}

.scroll-y-transition-leave-to {
  transform: translateY(15px)
}

.scroll-y-reverse-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-reverse-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-reverse-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scroll-y-reverse-transition-enter-from, .scroll-y-reverse-transition-leave-to {
  opacity: 0
}

.scroll-y-reverse-transition-enter-from {
  transform: translateY(15px)
}

.scroll-y-reverse-transition-leave-to {
  transform: translateY(-15px)
}

.scroll-x-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-transition-enter-from, .scroll-x-transition-leave-to {
  opacity: 0
}

.scroll-x-transition-enter-from {
  transform: translateX(-15px)
}

.scroll-x-transition-leave-to {
  transform: translateX(15px)
}

.scroll-x-reverse-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-reverse-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-reverse-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.scroll-x-reverse-transition-enter-from, .scroll-x-reverse-transition-leave-to {
  opacity: 0
}

.scroll-x-reverse-transition-enter-from {
  transform: translateX(15px)
}

.scroll-x-reverse-transition-leave-to {
  transform: translateX(-15px)
}

.slide-x-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-transition-enter-from, .slide-x-transition-leave-to {
  opacity: 0;
  transform: translateX(-15px)
}

.slide-x-reverse-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-reverse-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-reverse-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.slide-x-reverse-transition-enter-from, .slide-x-reverse-transition-leave-to {
  opacity: 0;
  transform: translateX(15px)
}

.fade-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.fade-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.fade-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.fade-transition-enter-from, .fade-transition-leave-to {
  opacity: 0 !important
}

.fab-transition-enter-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.fab-transition-leave-active {
  transition: .3s cubic-bezier(.4, 0, .2, 1)
}

.fab-transition-move {
  transition: transform .5s cubic-bezier(.4, 0, .2, 1)
}

.fab-transition-enter-from, .fab-transition-leave-to {
  transform: scale(0) rotate(-45deg)
}

</style>
