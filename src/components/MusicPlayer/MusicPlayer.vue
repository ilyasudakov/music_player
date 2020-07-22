<template>
  <div class="music-player">
    <div class="music-player__cover-art">
      <transition-group :name="transitionName">
        <div
          class="cover-art__item"
          :style="{ backgroundImage: `url(${track.cover})` }"
          v-for="track in tracks.filter(
            (track, index) => index === currentTrackIndex,
          )"
          :key="track.source"
        ></div>
      </transition-group>
    </div>
    <div class="music-player__content">
      <div class="music-player__song-info" v-if="currentTrack">
        <div class="song-info__song-name">{{ currentTrack.name }}</div>
        <div class="song-info__artist">{{ currentTrack.artist }}</div>
      </div>
      <div class="music-player__controls">
        <div @click="prevSong" class="controls__item">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            focusable="false"
            width="1em"
            class="icon"
            height="1em"
            style="
              -ms-transform: rotate(360deg);
              -webkit-transform: rotate(360deg);
              transform: rotate(360deg);
            "
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <path d="M16 7l-7 5l7 5zm-7 5V7H7v10h2z" fill="#626262" />
            <rect x="0" y="0" width="24" height="24" fill="rgba(0, 0, 0, 0)" />
          </svg>
        </div>
        <div @click="play" class="controls__item">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            class="icon icon--play"
            focusable="false"
            width="1em"
            height="1em"
            v-if="isTimerPlaying"
            style="
              -ms-transform: rotate(360deg);
              -webkit-transform: rotate(360deg);
              transform: rotate(360deg);
            "
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <path d="M8 7h3v10H8zm5 0h3v10h-3z" fill="#626262" />
            <rect x="0" y="0" width="24" height="24" fill="rgba(0, 0, 0, 0)" />
          </svg>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            focusable="false"
            v-if="!isTimerPlaying"
            class="icon icon--play"
            width="1em"
            height="1em"
            style="
              -ms-transform: rotate(360deg);
              -webkit-transform: rotate(360deg);
              transform: rotate(360deg);
            "
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <path d="M7 6v12l10-6z" fill="#626262" />
            <rect x="0" y="0" width="24" height="24" fill="rgba(0, 0, 0, 0)" />
          </svg>
        </div>
        <div @click="nextSong" class="controls__item">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            focusable="false"
            width="1em"
            height="1em"
            class="icon"
            style="
              -ms-transform: rotate(360deg);
              -webkit-transform: rotate(360deg);
              transform: rotate(360deg);
            "
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <path d="M7 7v10l7-5zm9 10V7h-2v10z" fill="#626262" />
            <rect x="0" y="0" width="24" height="24" fill="rgba(0, 0, 0, 0)" />
          </svg>
        </div>
      </div>
      <div class="progress">
        <div class="progress__cur-duration">{{ currentTime }}</div>
        <div class="progress__bar" @click="clickProgress" ref="progress">
          <div class="progress__current" :style="{ width: barWidth }"></div>
        </div>
        <div class="progress__song-length">{{ duration }}</div>
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
          name: 'Rear View Mirror',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/rear_view_mirror.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/rear_view_mirror.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: `Don't Leave Me Now`,
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/dont_leave_me_now.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/dont_leave_me_now.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Perfect Danger',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/perfect_danger.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/perfect_danger.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Always Watch Your Back',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/always_watch_your_back.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/always_watch_your_back.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Sunset Extra',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/sunset_extra.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/sunset_extra.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Interference',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/interference.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/interference.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Remedia',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/remedia.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/remedia.mp3',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
        },
        {
          name: 'Penultima',
          artist: 'boilerplate',
          cover:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/penultima.png',
          source:
            'https://raw.githubusercontent.com/ilyasudakov/music_player/master/src/assets/penultima.mp3',
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
      this.currentTrackIndex === this.tracks.length - 1
        ? (this.currentTrackIndex = 0)
        : this.currentTrackIndex++
      this.currentTrack = this.tracks[this.currentTrackIndex]
      this.resetPlayer()
    },
    resetPlayer() {
      this.barWidth = 0
      this.circleLeft = 0
      this.audio.currentTime = 0
      this.audio.src = this.currentTrack.source
      setTimeout(() => {
        this.isTimerPlaying ? this.audio.play() : this.audio.pause()
      }, 300)
    },
    prevSong() {
      this.transitionName = 'scale-out'
      this.currentTrackIndex > 0
        ? this.currentTrackIndex--
        : (this.currentTrackIndex = this.tracks.length - 1)
      this.currentTrack = this.tracks[this.currentTrackIndex]
      this.resetPlayer()
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
      let durmin = Math.floor((this.audio.duration || 0) / 60)
      let dursec = Math.floor((this.audio.duration || 0) - durmin * 60)
      let curmin = Math.floor((this.audio.currentTime || 0) / 60)
      let cursec = Math.floor((this.audio.currentTime || 0) - curmin * 60)
      if (dursec < 10) {
        dursec = '0' + dursec
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
      playerData.nextSong()
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
  max-width: 325px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #ffffff;
  justify-content: center;
  // padding: 15px;
  box-shadow: 0px 10px 10px 0px rgba(76, 70, 124, 0.3);
  z-index: 5;

  &__content {
    margin-top: 25px;
    width: 100%;
    // background-color: rgba($color: #ffffff, $alpha: 0.3);
    background-color: rgba($color: #ffffff, $alpha: 0.5);
    border: 1px solid #cccccc;
    // backdrop-filter: blur(25px);
    box-shadow: 0px 10px 10px 0px rgba(76, 70, 124, 0.1);
    border-radius: 15px;
    padding: 15px;
    z-index: 0;
  }

  &__cover-art {
    width: calc(100% - 10px);
    // width: 100%;
    top: 5px;
    // width: 100%;
    height: 250px;
    margin-bottom: 5px;
    position: relative;
    z-index: -1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .cover-art__item {
      background-color: rgba($color: #bbbbbb, $alpha: 0.3);
      background-repeat: no-repeat;
      background-position: center;
      background-size: cover;
      width: calc(100% - 0px);
      height: 100%;
      border-radius: 15px;
      position: absolute;
      left: 0;
      top: 0;

      &:before {
        content: '';
        background: inherit;
        width: 100%;
        height: 100%;
        display: block;
        z-index: 1;
        position: absolute;
        top: calc(100% - 10px);
        transform: scale(1);
        filter: blur(15px);
        opacity: 0.5;
        border-radius: 15px;
      }

      &:after {
        content: '';
        background: inherit;
        width: 100%;
        height: 100%;
        display: block;
        z-index: 2;
        position: absolute;
        border-radius: 15px;
      }
    }
  }

  &__song-info {
    width: 100%;
    font-size: 2rem;
    color: #555555;

    .song-info__song-name {
      font-size: 90%;
      max-width: 100%;
      white-space: nowrap;
      text-overflow: ellipsis;
      overflow-x: hidden;
    }
    .song-info__artist {
      font-size: 70%;
      color: #777777;
      max-width: 100%;
      white-space: nowrap;
      text-overflow: ellipsis;
      overflow-x: hidden;
    }
  }

  .progress {
    width: 100%;
    // max-width: calc(100% - 25px);
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    color: #999999;
    font-size: 90%;
    // z-index: 0;

    .progress__bar {
      height: 6px;
      width: 100%;
      cursor: pointer;
      background-color: #d0d8e6;
      display: inline-block;
      border-radius: 10px;
      transition: 100ms ease-in-out;

      &:hover {
        height: 12px;
      }
      .progress__current {
        height: inherit;
        width: 0%;
        background-color: #a3b3ce;
        border-radius: 10px;
      }
    }

    &__cur-duration {
      padding: 5px;
      padding-right: 10px;
    }
    &__song-length {
      padding: 5px;
      padding-left: 10px;
    }
  }

  &__controls {
    display: flex;
    flex-direction: row;
    // justify-content: flex-start;
    // justify-content: space-between;
    justify-content: center;
    align-items: center;
    width: 100%;
    // margin: 5px 0;

    .controls__item {
      // margin-right: 10px;
      cursor: pointer;
      border-radius: 999px;

      &::before {
        width: 10px;
        height: 10px;
        transition: 100ms ease-in-out;
        background-color: red;
        z-index: 999;
        // transform: scale(0);
        // z-index: 1;
      }

      .icon {
        width: 45px;
        height: 45px;
        fill: #a3b3ce;

        &--play {
          width: 80px;
          height: 80px;
        }
      }

      &:hover {
        &::before {
          // transform: scale(1);
        }

        .icon {
          fill: #333 !important;
        }
      }
    }
  }

  @media (max-width: 768px) {
    & {
      max-width: 300px;

      &__cover-art {
        margin-bottom: 5px;

        .cover-art__item {
          &::before {
            top: calc(100% - 40px) !important;
          }
        }
      }

      &__song-info {
        font-size: 1.5rem;
      }

      &__controls {
        .icon {
          &--play {
            width: 70px !important;
            height: 70px !important;
          }
        }
      }

      .progress {
        &__bar {
          height: 0.8rem !important;
        }
      }
    }
  }

  //scale out

  .scale-out-enter-active {
    transition: all 0.35s ease-in-out;
  }
  .scale-out-leave-active {
    transition: all 0.35s ease-in-out;
  }
  .scale-out-enter {
    transform: scale(0.55);
    // transform: translateX(1);
    pointer-events: none;
    opacity: 0;
  }
  .scale-out-leave-to {
    transform: scale(1.2);
    pointer-events: none;
    opacity: 0;
  }

  //scale in

  .scale-in-enter-active {
    transition: all 0.35s ease-in-out;
  }
  .scale-in-leave-active {
    transition: all 0.35s ease-in-out;
  }
  .scale-in-enter {
    // transform: scale(1.2);
    transform: translateX(1);
    pointer-events: none;
    opacity: 0;
  }
  .scale-in-leave-to {
    // transform: scale(0.55);
    transform: translateX(1);
    pointer-events: none;
    opacity: 0;
  }
}
</style>
