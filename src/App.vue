<template>
  <div class="site">
    <main>
      <div class="box title">
        <img class="logo" src="./assets/logo.png" />
        <hr />
        <div class="art" :style="{ backgroundImage: `url('${image}')` }">
          <div class="box controls">
            <span
              v-on:click="toggle"
              class="play-icon"
              v-bind:class="{ pause: isPlaying }"
            ></span>
          </div>
        </div>
        <h1 id="artist">{{ artist }}</h1>
        <h2 id="song">{{ song }}</h2>
        <hr />
      </div>
    </main>
    <footer>
      <small>&copy; Radio Terminal. Vse pravice pridržane.</small>
    </footer>
  </div>
</template>

<script>
import * as rt from "@/rt.service";
const STATUS_INITIAL = 0,
  STATUS_PLAYING = 1,
  STATUS_STOPPED = 2;

var audio = new Audio();
var hls = new Hls();
export default {
  name: "app",
  data() {
    return {
      currentStatus: null,
      image: null,
      artist: null,
      song: null,
    };
  },
  computed: {
    isPlaying() {
      return this.currentStatus === STATUS_PLAYING;
    },
  },
  methods: {
    wire() {
      var url = "https://stream.radioterminal.si/hls/live.m3u8";
      var url2 = "https://air.radioterminal.si/live";
      if (Hls.isSupported()) {
        hls.loadSource(url);
        hls.attachMedia(audio);
      } else if (audio.canPlayType("application/vnd.apple.mpegurl")) {
        audio.src = url2;
      }
      audio.addEventListener("play", (e) => {
        this.currentStatus = STATUS_PLAYING;
      });
      audio.addEventListener("stop", (e) => {
        this.currentStatus = STATUS_STOPPED;
      });
      audio.addEventListener("pause", (e) => {
        this.currentStatus = STATUS_STOPPED;
      });
    },
    toggle() {
      if (this.currentStatus === STATUS_PLAYING) {
        audio.pause();
        hls.stopLoad();
        audio = new Audio();
        hls = new Hls();
      } else {
        this.wire();
        audio.play();
      }
    },
    list() {
      // reset form to initial state
      rt.now().then((x) => {
        this.image = x.slika;
        this.artist = x.artist;
        this.song = x.track;
      });
    },
  },
  mounted() {
    this.list();
    setInterval(this.list, 5000);
  },
};
</script>

<style lang="scss">
body {
  margin: 0;
  background: #fff;
  overflow: hidden;
  font-family: "Roboto", Arial, sans-serif;
  color: #333333;
}
.art {
  min-width: 300px;
  min-height: 300px;
  background-repeat: no-repeat;
  background-position: 50% 10%;
  background-size: cover;
  background-origin: padding-box;
  background-attachment: scroll;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgb(231, 231, 231);
  background-color: radial-gradient(
    circle,
    rgba(231, 231, 231, 0.6735644941570378) 0%,
    rgba(188, 188, 188, 0.5895308807116597) 100%
  );
}
.logo {
  width: 100%;
  background-repeat: no-repeat;
  background-position: 50% 10%;
  background-size: contain;
  background-origin: padding-box;
  background-attachment: scroll;
}
footer {
  display: flex;
  flex-direction: column;
}
main hr {
  background-color: #333333;
  height: 6px;
}

.box {
  margin: 1rem;
  input,
  audio {
    display: none;
  }
}

#song {
  font-size: 18pt;
  margin-bottom: 0;
  font-weight: 500;
  margin-top: 0;
}

#artist {
  font-size: 22pt;
  margin: 0;
  font-weight: 500;
}

.play-icon {
  width: 80pt;
  height: 80pt;
  overflow: hidden;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  position: relative;
  -webkit-clip-path: polygon(0% 0%, 100% 50%, 100% 50%, 0% 100%);
  clip-path: polygon(0% 0%, 100% 50%, 100% 50%, 0% 100%);
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  cursor: pointer;
  will-change: clip-path;
  -webkit-transform: translateZ(0);
  transform: translateZ(0);
}
.play-icon:active:before,
.play-icon:active:after {
  -webkit-transition: none;
  transition: none;
}
.play-icon:before,
.play-icon:after {
  background-color: rgba(255, 255, 255, 0.907);
  display: block;
  content: "";
  height: 100%;
  width: 50%;
}
.play-icon:after {
  margin-left: 0%;
}
.play-icon,
.play-icon:after {
  -webkit-transition: all 250ms ease;
  transition: all 250ms ease;
}
.play-icon.pause {
  -webkit-clip-path: polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%);
  clip-path: polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%);
}
.play-icon.pause:after {
  margin-left: 12.5%;
}
footer {
  margin-left: 20px;
  margin-bottom: 20px;
}
small {
  font-size: 8px;
}
</style>
