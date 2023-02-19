<template>
  <div>
    <div class="overlay"></div>  
    <div class="back-1"></div>
    <div class="back-2"></div>
    <div class="logo">
        <span>url.</span><span>Shrtr</span>
        <div class="copy">
            &copy; <span><a href="https://moatasimashraf.000webhostapp.com" target="_blank">Moatasim</a></span>
        </div>
    </div>
    <div class="content">
      <div class="main">
        <form>
          <input type="text" v-model.lazy="inputUrl" @focus="shortenedUrl = '', loaderShown = false" placeholder="Enter a Valid URL" autofocus>
          <button @click.prevent="getShortUrl(inputUrl),loaderShown = true">Shorten</button>
          <button @click.prevent="flipContent(), this.showResult = false" class="history"><font-awesome-icon icon="fa-solid fa-clock-rotate-left" /></button>
        </form>
        <div v-if="showResult" class="result">
          <span v-if="inputUrl">The URL: </span>
          <p>{{ inputUrl }}</p>
          <div v-if="loaderShown && !shortenedUrl" class="loader"></div>
          <span v-if="shortenedUrl && inputUrl">The Shortened URL: </span>
          <p v-if="shortenedUrl && inputUrl">{{ shortenedUrl }}</p>
        </div>
        <form v-if="shortenedUrl && inputUrl" class="clipboard">
        <!-- <form class="clipboard"> -->
          <!-- The button used to copy the text -->
          <button @click.prevent="copyingMethod()">
            <p>Copy to Clipboard</p>
            <p><svg fill="white" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 512"><path d="M562.8 267.7c56.5-56.5 56.5-148 0-204.5c-50-50-128.8-56.5-186.3-15.4l-1.6 1.1c-14.4 10.3-17.7 30.3-7.4 44.6s30.3 17.7 44.6 7.4l1.6-1.1c32.1-22.9 76-19.3 103.8 8.6c31.5 31.5 31.5 82.5 0 114L405.3 334.8c-31.5 31.5-82.5 31.5-114 0c-27.9-27.9-31.5-71.8-8.6-103.8l1.1-1.6c10.3-14.4 6.9-34.4-7.4-44.6s-34.4-6.9-44.6 7.4l-1.1 1.6C189.5 251.2 196 330 246 380c56.5 56.5 148 56.5 204.5 0L562.8 267.7zM43.2 244.3c-56.5 56.5-56.5 148 0 204.5c50 50 128.8 56.5 186.3 15.4l1.6-1.1c14.4-10.3 17.7-30.3 7.4-44.6s-30.3-17.7-44.6-7.4l-1.6 1.1c-32.1 22.9-76 19.3-103.8-8.6C57 372 57 321 88.5 289.5L200.7 177.2c31.5-31.5 82.5-31.5 114 0c27.9 27.9 31.5 71.8 8.6 103.9l-1.1 1.6c-10.3 14.4-6.9 34.4 7.4 44.6s34.4 6.9 44.6-7.4l1.1-1.6C416.5 260.8 410 182 360 132c-56.5-56.5-148-56.5-204.5 0L43.2 244.3z"/></svg></p>
            <p v-if="false"><font-awesome-icon icon="fa-solid fa-link"/></p>
          </button>
        </form>
      </div>
      <div class="list">
        <ul>
          <div class="go-back" @click="flipContent(),waitToShowResult()"><font-awesome-icon icon="fa-solid fa-arrow-left" /></div>
          <li v-for="i in 3" :key="i" class="result">
            <span v-if="true">The URL: </span>
            <p>{{ historyLinks[Object.keys(historyLinks).length - (i) + 1].longUrl }}</p>
            <span v-if="true">The Shortened URL: </span>
            <p v-if="true">{{ historyLinks[Object.keys(historyLinks).length - (i) + 1].shortUrl }}</p>
          </li>
        </ul>
      </div>
    </div>
    </div>
</template>
<script>
/* Set up using Vue 3 */
import { createApp } from 'vue'
import App from './App.vue'

/* import the fontawesome core */
import { library } from '@fortawesome/fontawesome-svg-core'

/* import font awesome icon component */
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

/* import specific icons */
import { faArrowLeft, faClockRotateLeft, faLink } from '@fortawesome/free-solid-svg-icons'

/* add icons to the library */
library.add(faLink,faClockRotateLeft,faArrowLeft)

createApp(App)
.component('font-awesome-icon', FontAwesomeIcon)
.mount('#app')
export default {
  name: 'App',
  data(){
    return{
      inputUrl:'',
      shortenedUrl:'',
      loaderShown:false,
      showResult: true,
      historyLinks:{
        "1":{
          longUrl:'No Links Added Yet',
          shortUrl:'No Links Added Yet',
        },
        "2":{
          longUrl:'No Links Added Yet',
          shortUrl:'No Links Added Yet',
        },
        "3":{
          longUrl:'No Links Added Yet',
          shortUrl:'No Links Added Yet',
        }
      }
    }
  },
  components:{
    FontAwesomeIcon,
  },
  mounted(){
    if(window.localStorage.getItem("historyLinks")){
      this.historyLinks = JSON.parse(window.localStorage.getItem("historyLinks"));
    }
  },
  methods:{
    getShortUrl(urlLong){
        const url = new URL(
          "https://t.ly/api/v1/link/shorten"
      );
      const params = {
          "api_token": "GDS9PgsW3oHaSAhnyjgK2Wr9gHMtP6kkFWfMGY9QpPxU6G5wdY47aUWxu5Pd",
      };
      Object.keys(params)
          .forEach(key => url.searchParams.append(key, params[key]));

      const headers = {
          "Content-Type": "application/json",
          "Accept": "application/json",
      };

      let body = {
          "long_url": urlLong,
          // "domain": "https:\/\/t.ly\/",
          // "include_qr_code": false
      };

      fetch(url, {
          method: "POST",
          headers,
          body: JSON.stringify(body),
      }).then((response) => {
        return response.json();
      }).then((response) => {
        // response.json();
        console.log(response)
        console.log(response.short_url)
        if(response.message){
          this.shortenedUrl = response.message.split(".")[0];
        }else{
          this.shortenedUrl = response.short_url;
          this.rearrangeHistoryLinks();
          this.historyLinks[`${Object.keys(this.historyLinks).length}`].shortUrl = response.short_url;
          this.historyLinks[`${Object.keys(this.historyLinks).length}`].longUrl = urlLong;
          console.log(this.historyLinks)
          window.localStorage.setItem("historyLinks",JSON.stringify(this.historyLinks));
        }
        return response.short_url;
      })
    },
    rearrangeHistoryLinks(){
      this.historyLinks[1].shortUrl = this.historyLinks[2].shortUrl;
      this.historyLinks[1].longUrl = this.historyLinks[2].longUrl;
      this.historyLinks[2].shortUrl = this.historyLinks[3].shortUrl;
      this.historyLinks[2].longUrl = this.historyLinks[3].longUrl;
    },
    copyingMethod(){
      navigator.clipboard.writeText(this.shortenedUrl);
      alert("Copied the text: " + this.shortenedUrl);
    },
    flipContent(){
      console.log('flip')
      document.querySelector(".content").classList.toggle("flipped");
    },
    waitToShowResult(){
      setTimeout(()=>{this.showResult = true},500);
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Tourney:wght@200;300;400;500;600;700;800;900&display=swap');
*{
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
:root{
  --main-transition: all 0.1s 0s linear;
  --main-color:#cf278a;
  --main-color-semi:#cf278969;
  --main-color-dark:#1d2254;
  --sec-color:#2b9dd9;
  --sec-color-semi:#2b9cd92f;
}
body{
  /* background-color: rgb(15, 206, 155); */
  background-image: url(../imgs/vecteezy_gradient-luxury-with-pink-and-cyan-background_19479575.jpg);
  background-size: cover;
  background-repeat: no-repeat;
  min-height: 100vh;
  height: fit-content;
  min-height: 110vh;
}
#app {
  position: relative;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 110vh;
  /* padding-top: 200px; */
}
.overlay{
  /* display: none; */
  position: fixed;
  left: 0;
  top: 0;
  background-color: var(--main-color-dark);
  width: 100vw;
  height: 100vh;
  opacity: 0.7;
}
.back-1{
  position: fixed;
  left: 0;
  top: 0;
  width: 100vw;
  height: 100vh;
  background-image: url(../imgs/layered-waves-haikei.svg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  opacity: 0.7;
}
.back-2{
  position: fixed;
  left: 0;
  transform: rotateZ(180deg);
  top: 0;
  width: 100vw;
  height: 100vh;
  background-image: url(../imgs/layered-waves-haikei.svg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  opacity: 0.7;
}
#app > div:not(.overlay){
  position: relative;
}
#app > div{
  padding-top: 50px;
  height: 100vh;
}
.logo{
  position: relative;
  font-size: 40px;
  font-family: "Tourney",sans-serif;
  font-weight: 600;
  text-decoration: underline;
  text-decoration-color: var(--sec-color);
  width: fit-content;
  margin-left: auto;
  margin-right: auto;
}
.logo span:first-of-type{
  color:var(--sec-color);
  /* background-color: var(--main-color-semi); */
}
.logo span:last-of-type{
  color: var(--main-color);
  /* background-color: var(--sec-color-semi); */
}
.copy{
  color: var(--sec-color);
  position: absolute;
  font-size: 12px;
  left: 100%;
  bottom: 0px;
  display: flex; 
  gap: 3px  ;
  /* transform: translateX(-50%); */
}
.copy a{
  color: var(--main-color);
  text-decoration: none;
  text-transform: uppercase;
  font-weight: bold;
}
.content{
  position: relative;
  width: 500px;
  margin-right: auto;
  margin-left: auto;
  max-width: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.main{
  max-width: 100%;
}
form{
  position: relative;
  display: flex;
  gap: 10px;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  margin-top: 40px;
  padding-right: 20px;
  padding-left: 20px;
  transform: rotateY(0deg);
  transition: var(--main-transition);
  transition-timing-function: ease-in-out;
  transition-duration: 0.5s;
  max-width: 100%;
}
form input,
form button{
  padding: 5px 10px;
  font-size: 18px;
  border-radius: 6px;
}
form input{
  width: 500px;
  background-color: var(--sec-color);
  transition: var(--main-transition);
  color: white;
  border: 2px solid var(--sec-color);
}
form input::placeholder{
  color: rgb(229, 225, 225);
}
form button{
  cursor: pointer;
  background-color: var(--main-color);
  color: white;
  border: 2px solid var(--main-color);
  transition: var(--main-transition);
}
form button.history{
  display: flex;
  align-items: center;
  justify-content: center;
}
form input:focus,
form button:focus{
  outline: none;
}
form button:hover,
form button:focus{
  box-shadow: 2px 2px 20px 5px var(--main-color);
}
form input:focus{
  box-shadow: 2px 2px 20px 5px var(--sec-color);
}
.content .list{
  /* transform: translateY(-60px) rotateY(0deg); */
  padding-left: 20px;
  padding-right: 20px;
  transform: translateY(-60px) rotateY(90deg);
  transition: var(--main-transition);
  transition-timing-function: ease-in-out;
  opacity: 0;
  transition-duration: 0.5s;
  max-width: 100%;
  min-width: 300px;
  display: block;
  overflow: hidden;
  height: 0;
}
.content .list ul{
  list-style: none;
  border-right: 1px solid var(--main-color);
  border-left: 1px solid var(--main-color);
  background-color: var(--main-color-dark);
  border-radius: 20px;
  transition-delay: 0.3s;
  max-height: 100%;
  overflow-y: scroll;
  position: relative;
}
.content .list ul::-webkit-scrollbar{
  background-color: var(--main-color-dark);
  border-radius: 10px;
  width: 10px;
}
.content .list ul::-webkit--track{
  /* background-color: var(--sec-color); */
}
.content .list ul::-webkit-scrollbar-thumb{
  background-color: var(--main-color);
  border-radius: 10px;
}
.content .list ul .go-back{
  background-color: var(--main-color);
  padding: 5px 10px;
  border-radius: 6px;
  position: absolute;
  left: 20px;
  top: 10px;
  z-index: 2;
  cursor:pointer;
}
.content .list ul svg{
  color: var(--main-color-dark);  
}
.content .list ul li{
  opacity: 0;
  transform: translateX(20px);
  padding-top: 20px;
  padding-bottom: 20px;
  transition: var(--main-transition);
  transition-duration: 0.3s;
  transition-delay: 0.5s;
  filter: blur(20px);
}
.content .list ul li:nth-of-type(2){
  transition-delay: 0.7s;
}
.content .list ul li:nth-of-type(3){
  transition-delay: 0.9s;
}
.content .list ul li:not(:last-child){
  border-bottom: 2px solid var(--main-color);
}
.content.flipped form{
  transform: rotateY(-90deg);
  opacity: 0;
}
.content.flipped .list{
  height: 580px;
  opacity: 1;
  transform: translateY(-60px) rotateY(0deg);
}
.content.flipped .list li{
  filter: blur(0px);
  opacity: 1;
  transform: translateX(0px);
}
.content .list .result p{
  font-size: 16px !important;
}
.content .list .result span{
  font-size: 14px !important;
}
/* .content:hover form{
  transform: rotateY(-90deg);
}
.content:hover .list{
  transform: translate(-50%) rotateY(0deg);
} */
@media (max-width:600px) {
  form{
    flex-direction: column;
  }
  form input{
    max-width: 100%;
  }
}
#app .result{
  position: relative;
  color: white;
}
#app .result p{
  margin-top: 10px;
  margin-bottom: 20px;
  font-size: 20px;
  color: var(--sec-color);
  padding-left: 20px;
  overflow-wrap: break-word;
  padding-right: 20px;
}
#app .main .result p{
  font-size: 20px;
}
#app .result .loader{
  position: relative;
  width: fit-content;
  margin:30px auto;
}
#app .result .loader::before{
  content: '';
  position: absolute;
  z-index: 3;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 3px solid var(--main-color);
  border-right: none;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  animation: loader 1s linear 0s infinite alternate;
}
#app .result .loader::after{
  content: '';
  position: absolute;
  z-index: 3;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 3px solid var(--sec-color);
  border-left: none;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  animation: loader 1s linear 0.2s infinite alternate-reverse;
}
@keyframes loader {
  0%{
    width: 30px;
    height: 30px;
    transform: translate(-50%,-50%) rotateZ(0);
  }
  100%{
    width: 0px;
    transform: translate(-50%,-50%) rotateZ(360deg);
    height: 0px;
  }
}
.clipboard{
  position: relative;
}
.clipboard button{
  font-size: 14px;
  display: flex;
  flex-direction: row;
  gap: 10px;
  align-items: center;
  justify-content: center ;
}
.clipboard button svg{
  width: 20px;
}
.clipboard button p:has(svg){
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center ;
}

</style>
