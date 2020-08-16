<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    >
      <div class="text-center">
        <logo />
        <vuetify-logo />
      </div>
      <v-text-field
            label="Regular"
            v-model="model"
          ></v-text-field>
          <div>{{ model }}</div> 
          <v-btn @click="greet">search</v-btn>
          
<v-list>
      <v-subheader>REPORTS</v-subheader>
      <v-list-item-group v-model="item" color="primary">
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
        >
          
          <v-list-item-content>
            <v-list-item-title v-text="item.snippet.title"></v-list-item-title>
            <div v-if="rates[i]">{{item.data.items[0].statistics.likeCount}}</div>
            <div v-if="rates[i]">{{calculate(item.data.items[0].statistics.likeCount, item.data.items[0].statistics.dislikeCount)}}</div>
            <v-img :src="item.snippet.thumbnails.default.url"></v-img>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
      <v-card>
        <v-card-title class="headline">
          Kaoru to the Vuetify + Nuxt.js template
        </v-card-title>
        <v-card-text>
          <p>Vuetify is a progressive Material Design component framework for Vue.js. It was designed to empower developers to create amazing applications.</p>
          <p>
            For more information on Vuetify, check out the <a
              href="https://vuetifyjs.com"
              target="_blank"
              rel="noopener noreferrer"
            >
              documentation
            </a>.
          </p>
          <p>
            If you have questions, please join the official <a
              href="https://chat.vuetifyjs.com/"
              target="_blank"
              rel="noopener noreferrer"
              title="chat"
            >
              discord
            </a>.
          </p>
          <p>
            Find a bug? Report it on the github <a
              href="https://github.com/vuetifyjs/vuetify/issues"
              target="_blank"
              rel="noopener noreferrer"
              title="contribute"
            >
              issue board
            </a>.
          </p>
          <p>Thank you for developing with Vuetify and I look forward to bringing more exciting features in the future.</p>
          <div class="text-xs-right">
            <em><small>&mdash; John Leider</small></em>
          </div>
          <hr class="my-3">
          <a
            href="https://nuxtjs.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            Nuxt Documentation
          </a>
          <br>
          <a
            href="https://github.com/nuxt/nuxt.js"
            target="_blank"
            rel="noopener noreferrer"
          >
            Nuxt GitHub
          </a>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/inspire"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import axios from 'axios'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    Logo,
    VuetifyLogo
  },
  data: () => ({
      model: 'I\'m a text field',
      items: [],
      item: 0,
      rates: [],
    }),
  methods: {
    greet: async function (event) {
      //axios.get(`https://www.googleapis.com/youtube/v3/search?key=AIzaSyB8WpIBgH2_E3zwmZrJMGq0Dc8DYrCrOqM&part=snippet&q=${this.model}&type=video&maxResults=50`)
      axios.get(`https://www.googleapis.com/youtube/v3/search?key=AIzaSyAAjY2RHYpFNXjTiXdEsxDky2_wu5T40Fk&part=snippet&q=${this.model}&type=video&maxResults=50`)//島ちゃんからもらったapiキー
    .then(async(result) => {
      this.items=result.data.items
      console.log(this.items)
      this.rates=
      await Promise.all(
      this.items.map(async(item)=>{
        return await this.getrate(item.id.videoId)//statisticsの情報が返ってくる
      }))
      console.log(this.rates)
      this.items.map(async(item, i)=>{

        Object.assign(item, this.rates[i])
      })
      console.log(this.items)
      console.log("こんにちは")
      this.items.sort((a,b) =>{//ソート処理
        if(
          this.calculate(a.data.items[0].statistics.likeCount, a.data.items[0].statistics.dislikeCount)<
          this.calculate(b.data.items[0].statistics.likeCount, b.data.items[0].statistics.dislikeCount)
        ){
          return 1
        }else{
          return -1
        }
      })
    })
    .catch((err) => {
      console.warn('Error', err)
    })
    },
    getrate: async function (id) {
      console.log(id)
      //return await axios.get(`https://www.googleapis.com/youtube/v3/videos?key=AIzaSyB8WpIBgH2_E3zwmZrJMGq0Dc8DYrCrOqM&part=statistics&id=${id}`)
      return await axios.get(`https://www.googleapis.com/youtube/v3/videos?key=AIzaSyAAjY2RHYpFNXjTiXdEsxDky2_wu5T40Fk&part=statistics&id=${id}`)//島ちゃんからもらったapiキー
      
    },
    calculate: function(like, dislike){//得点計算
      let result = (Number(like)/(Number(like)+Number(dislike)))-1/(2*Math.sqrt(Number(like)+Number(dislike)))
      result = Math.round(result*10000)/100
      return result
    } 
  },
}
</script>
