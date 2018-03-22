<template>
  <div>
    <div style="position: absolute; z-index: 100; display: inline-block; width: 100%;">
      <div class="container-left" @click="pressLeft">
      </div>
      <div class="container-right" @click="pressRight">
      </div>
    </div>
    <transition appear name="fade">
    <div class="info text" v-bind:key="currentArticle" v-if="multiData[currentVendor]">
      <a :href="multiData[currentVendor].articles[currentArticle].url">
        <span class="header-text">
          {{ multiData[currentVendor].articles[currentArticle].title }}
        </span>
      <br>
        {{ multiData[currentVendor].articles[currentArticle].description }}
      </a><br>
      <small>
          {{ multiData[currentVendor].articles[currentArticle].source.name }}
      </small>
    </div>
    </transition>
    <transition appear name="mainFade">
      <div class="main" v-if="multiData[currentVendor]" v-bind:key="currentArticle" v-bind:style="{ 'background-image': 'url(\'' + multiData[currentVendor].articles[currentArticle].urlToImage + '\')' }">
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      API_Key: '86fa2caa5dac471a98d05dfa2d141b6f',
      data: [],
      multiData: [],
      vendors: [
        'mtv-news',
        'the-verge',
        'recode',
        'polygon',
        'bbc-news',
        'national-geographic'
      ],
      currentIndex: 0,
      currentVendor: 0,
      currentArticle: 0,
      show: true,
    }
  },
  created () {
    this.getArticles('mtv-news')
  },
  methods: {
    getArticles: function (source) {
      let self = this
      self.data = [{}]

      this.vendors.forEach(function(vendorName) {
        axios.get('https://newsapi.org/v2/everything?sources=' + vendorName + '&apiKey=86fa2caa5dac471a98d05dfa2d141b6f')
        .then(response => {
          let articles = response.data.articles
          let multiArticles = response.data.multiData
          
          articles.forEach(function (item, index) {
            Vue.set(self.data, index, item)
          })

          self.multiData.push({
            vendor: vendorName,
            articles: articles
          })
        })
        .catch(e => {
          console.log(e)
          // this.errors.push(e)
        })
      })
      

    },
    pressRight: function () {
      if (this.currentArticle === (this.multiData[this.currentVendor].articles.length - 1)) {
        this.currentArticle = 0
      } else {
        this.currentArticle++
      }

      // this.show = !this.show;
      // this.show = !this.show;
    },
    pressLeft: function () {
      if (this.currentArticle === 0) {
        this.currentArticle = (this.multiData[this.currentVendor].articles.length - 1)
      } else {
        this.currentArticle--
      }
    },
    pressDown: function () {
      if (this.currentVendor === 0) {
        this.currentVendor = (this.multiData.length - 1)
      } else {
        this.currentVendor--
      }

      console.log('Up')
    },
    pressUp: function () {
      if (this.currentVendor === (this.multiData.length - 1)) {
        this.currentVendor = 0
      } else {
        this.currentVendor++
      }
      
      console.log('Down')
    },
  },
  mounted: function () {
    window.addEventListener("keydown", e => {
      if (e.keyCode === 39) {
        this.pressRight()
      } else if (e.keyCode === 37) {
        this.pressLeft()
      }
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: #FFF;
  text-decoration: none;
}

a:hover {
  /* text-decoration: underline; */
  /* border-bottom: 1px solid #FFF;
  padding-bottom: .001rem; */
}

.main {
  z-index: 1;
  background-color: #333;
  position: absolute;
  overflow: hidden;
  top:0;
  bottom:0;
  width: 100%;
  background-position: center center;
}

.container-left, .container-right {
  width: 50%;  
  height: 100vh; 
  display: inline-block;
}

.container-left:hover {
  cursor: nw-resize;
}

.container-right:hover {
  cursor: ne-resize;
}

.info {
  z-index: 1000;
  background-color: rgba(0, 0, 0, .6);
  padding: 10px;
  display: inline-block;
  position: absolute;
  margin: 10px;
  color: #FFF;

  top:0;
  /* bottom:0; */
  border-radius: 4px;
}

.text {
  font-size: 14px;
}

.header-text {
  text-transform: uppercase;
  font-weight: 700;
  font-size: 16px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
  transition: all 1s ease;
  /* transform: translateX(60px); */
}

.fade-enter {
  opacity: 0;
  transform: translateX(70px)
}

.fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;

  transition: all 1s ease;
  transform: translateY(60px);
}


.mainFade-enter-active {
  transition: all 1s ease;
}
.mainFade-leave-active {
  transition: opacity .5s;
}
.mainFade-enter {
  opacity: 0;
  transition: all 1s ease;
  transform: translateX(30px) scale(1.1);
  /* box-shadow: 0 0 40px 40px black inset; */
  -webkit-filter: blur(10px);
  -moz-filter: blur(10px);
  -o-filter: blur(10px);
  -ms-filter: blur(10px);
  filter: blur(10px);
  transform: ;
}

.mainFade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
  transition: all 1s ease;
  /* transform: translateX(-30px); */
  /* -webkit-filter: blur(10px);
  -moz-filter: blur(10px);
  -o-filter: blur(10px);
  -ms-filter: blur(10px);
  filter: blur(10px); */
}
</style>
