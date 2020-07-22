<template>
  <div class="music-player">
    <div class="music-player__cover-art">
      <transition-group :name="transitionName">
        <div
          class="cover-art__item"
          :style="{ backgroundImage: `url(${track.cover})` }"
          v-for="(track, $index) in tracks"
          :key="$index"
        ></div>
      </transition-group>
    </div>
    <div class="music-player__song-info" v-if="currentTrack">
      <div>{{ currentTrack.name }}</div>
      <div>{{ currentTrack.artist }}</div>
    </div>
    <div class="music-player__controls">
      <div @click="prevSong">PREV</div>
      <div @click="play">PLAY</div>
      <div @click="nextSong">NEXT</div>
    </div>
    <div class="progress">
      <div class="progress__song-length">{{ duration }}</div>
      <div class="progress__cur-duration">{{ currentTime }}</div>
      <div class="progress__bar" @click="clickProgress" ref="progress">
        <div class="progress__current" :style="{ width: barWidth }"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MusicPlayer',
  data() {
    return {
      audio: null,
      circleLeft: null,
      barWidth: null,
      duration: null,
      currentTime: null,
      currentTrack: null,
      isTimerPlaying: false,
      currentTrackIndex: 0,
      transitionName: null,
      tracks: [
        {
          name: 'Sunset Extra',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/cover.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/sunset_extra.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Perfect Danget',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/perfect_danger.jpg',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/perfect_danger.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
      ],
    }
  },
  methods: {
    play() {
      if (this.audio.paused) {
        this.audio.play()
        this.isTimerPlaying = true
      } else {
        this.audio.pause()
        this.isTimerPlaying = false
      }
    },
    clickProgress(e) {
      this.isTimerPlaying = true
      this.audio.pause()
      this.updateBar(e.pageX)
    },
    nextSong() {
      this.transitionName = 'scale-in'
      if (this.currentTrackIndex > 0) {
        this.currentTrackIndex--
      } else this.currentTrackIndex = this.tracks.length - 1
      this.currentTrack = this.tracks[this.currentTrackIndex]
      this.resetPlayer()
    },
    resetPlayer() {
      this.barWidth = 0
      this.circleLeft = 0
      this.audio.currentTime = 0
      this.audio.src = this.currentTrack.source
      setTimeout(() => {
        if (this.isTimerPlaying) {
          this.audio.play()
        } else {
          this.audio.pause()
        }
      }, 300)
    },
    prevSong() {
      this.transitionName = 'scale-out'
    },
    updateBar(x) {
      let progress = this.$refs.progress
      let maxduration = this.audio.duration
      let position = x - progress.offsetLeft
      let percentage = (100 * position) / progress.offsetWidth
      if (percentage > 100) {
        percentage = 100
      }
      if (percentage < 0) {
        percentage = 0
      }
      this.barWidth = percentage + '%'
      this.circleLeft = percentage + '%'
      this.audio.currentTime = (maxduration * percentage) / 100
      this.audio.play()
    },
    generateTime() {
      let width = (100 / this.audio.duration) * this.audio.currentTime
      this.barWidth = width + '%'
      this.circleLeft = width + '%'
      let durmin = Math.floor(this.audio.duration / 60)
      let dursec = Math.floor(this.audio.duration - durmin * 60)
      let curmin = Math.floor(this.audio.currentTime / 60)
      let cursec = Math.floor(this.audio.currentTime - curmin * 60)
      if (durmin < 10) {
        durmin = '0' + durmin
      }
      if (dursec < 10) {
        dursec = '0' + dursec
      }
      if (curmin < 10) {
        curmin = '0' + curmin
      }
      if (cursec < 10) {
        cursec = '0' + cursec
      }
      this.duration = durmin + ':' + dursec
      this.currentTime = curmin + ':' + cursec
    },
  },
  created() {
    const playerData = this
    this.currentTrack = this.tracks[0]
    this.audio = new Audio()
    this.audio.src = this.currentTrack.source

    this.audio.ontimeupdate = function () {
      playerData.generateTime()
    }
    this.audio.onloadedmetadata = function () {
      playerData.generateTime()
    }
    this.audio.onended = function () {
      playerData.nextTrack()
      this.isTimerPlaying = true
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.music-player {
  width: 100%;
  height: fit-content;
  max-width: 500px;
  background-color: rgba($color: #333333, $alpha: 0.3);
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 15px;

  &__cover-art {
    width: 300px;
    height: 250px;

    .cover-art__item {
      background-repeat: no-repeat;
      background-position: center;
      background-size: cover;
      width: 100%;
      height: 100%;
      border-radius: 15px;
    }
  }

  .progress {
    width: 100%;
    .progress__bar {
      height: 6px;
      width: 100%;
      cursor: pointer;
      background-color: #d0d8e6;
      display: inline-block;
      border-radius: 10px;
    }
    .progress__current {
      height: inherit;
      width: 0%;
      background-color: #a3b3ce;
      border-radius: 10px;
    }
  }
}
</style>
