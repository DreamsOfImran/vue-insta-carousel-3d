<template>
  <div class="containerClass">
    <carousel-3d
      v-if="loading"
      :count="count"
      :perspective="perspective"
      :display="display"
      :loop="loop"
      :animation-speed="animationSpeed"
      :dir="direction"
      :width="width"
      :height="height"
      :border="border"
      :space="space"
      :start-index="startIndex"
      :clickable="clickable"
      :disable3d="disable3d"
      :min-swipe-distance="minSwipeDistance"
      :inverse-scaling="inverseScaling"
      :controls-visible="controlsVisible"
      :controls-prev-html="controlsPrevHtml"
      :controls-next-html="controlsNextHtml"
      :controls-width="controlsWidth"
      :controls-height="controlsHeight"
      :bias="bias"
      :on-main-slide-click="onMainSlideClick"
      :autoplay="autoplay"
      :autoplay-timeout="autoplayTimeout"
      :autoplay-hover-pause="autoplayHoverPause"
      @last-slide="onLastSlide"
      @after-slide-change="onSlideChange"
      @before-slide-change="beforeSlideChange"
    >
      <slide
        v-for="(image, i) in imageList"
        :key="i"
        :index="i"
      >
        <img
          :src="image.images.standard_resolution.url"
          class="slide-images"
        >
        <figcaption class="caption-container">
          <div class="caption-row">
            <div class="likes-col">
              {{ image.likes.count }}
            </div>
            <div class="comments-col">
              {{ image.comments.count }}
            </div>
          </div>
        </figcaption>
      </slide>
    </carousel-3d>
  </div>
</template>

<script>
import _ from 'lodash'
import axios from 'axios'
import Carousel3d from 'vue-carousel-3d/src/Carousel3d.vue'
import Slide from 'vue-carousel-3d/src/Slide.vue'

const noop = () => {
}

export default {
  name: 'VueInstaCarousel3d',
  components: {
    Carousel3d,
    Slide
  },

  props: {
    accessToken: {
      type: String,
      default: '8272330664.1677ed0.c558f1126b3f49019694d691a3a78fbb'
    },
    instaImageCount: {
      type: [Number, String],
      default: 50
    },
    showLikes: {
      type: Boolean,
      default: true
    },
    showComments: {
      type: Boolean,
      default: true
    },
    count: {
      type: [Number, String],
      default: 0
    },
    perspective: {
      type: [Number, String],
      default: 35
    },
    display: {
      type: [Number, String],
      default: 5
    },
    loop: {
      type: Boolean,
      default: true
    },
    animationSpeed: {
      type: [Number, String],
      default: 500
    },
    direction: {
      type: String,
      default: 'rtl'
    },
    width: {
      type: [Number, String],
      default: 360
    },
    height: {
      type: [Number, String],
      default: 270
    },
    border: {
      type: [Number, String],
      default: 1
    },
    space: {
      type: [Number, String],
      default: 'auto'
    },
    startIndex: {
      type: [Number, String],
      default: 0
    },
    clickable: {
      type: Boolean,
      default: true
    },
    disable3d: {
      type: Boolean,
      default: false
    },
    minSwipeDistance: {
      type: Number,
      default: 10
    },
    inverseScaling: {
      type: [Number, String],
      default: 300
    },
    controlsVisible: {
      type: Boolean,
      default: false
    },
    controlsPrevHtml: {
      type: String,
      default: '&lsaquo;'
    },
    controlsNextHtml: {
      type: String,
      default: '&rsaquo;'
    },
    controlsWidth: {
      type: [String, Number],
      default: 50
    },
    controlsHeight: {
      type: [String, Number],
      default: 50
    },
    onLastSlide: {
      type: Function,
      default: noop
    },
    onSlideChange: {
      type: Function,
      default: noop
    },
    beforeSlideChange: {
      type: Function,
      default: noop
    },
    bias: {
      type: String,
      default: 'left'
    },
    onMainSlideClick: {
      type: Function,
      default: noop
    },
    autoplay: {
      type: Boolean,
      default: false
    },
    autoplayTimeout: {
      type: Number,
      default: 2000
    },
    autoplayHoverPause: {
      type: Boolean,
      default: true
    }
  },

  data: () => ({
    error: '',
    loading: false,
    feeds: [],
    imageList: []
  }),

  mounted () {
    // this.getUserFeed()
    this.fetchInstaImages()
  },

  methods: {
    fetchInstaImages () {
      axios
        .get(
          `https://api.instagram.com/v1/users/self/media/recent?access_token=${this.accessToken}&count=${this.instaImageCount}`
        )
        .then(response => {
          this.imageList = response.data.data
          this.loading = true
        })
        .catch(e => {
          alert(e)
        })
    },
    getUserFeed () {
      this.loading = true
      axios
        .get('https://api.instagram.com/v1/users/self/media/recent', {
          params: { access_token: this.token, count: this.count }
        })
        .then(response => {
          this.loading = false
          if (response.data.meta.code === 400) this.error = response.data.meta
          if (response.data.meta.code === 200) {
            if (response.data.meta.code === 200) {
              let { data } = response.data
              const types = ['image', 'video']

              if (this.mediaType && types.indexOf(this.mediaType) > -1) {
                data = _.filter(data, item => this.mediaType === item.type)
              }

              if (this.tags.length) {
                data = _.filter(data, item => _.intersection(this.tags, item.tags).length)
              }

              this.feeds = _.slice(_.values(data), 0, this.count)
            }
          }
        })
        .catch(error => { throw error })
    }
  }
}
</script>

<style>
.slide-images {
  object-fit: cover;
  height: 100%;
}
.caption-container {
  position: absolute;
  background-color: rgba(0, 0, 0, 0.5);
  color: #fff;
  bottom: 0;
  position: absolute;
  bottom: 0;
  padding-top: 12px;
  padding-bottom: 12px;
  font-size: 12px;
  min-width: 100%;
  box-sizing: border-box;
}
</style>
