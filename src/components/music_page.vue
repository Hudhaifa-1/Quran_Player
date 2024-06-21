<script setup>
import { onMounted, ref } from 'vue'
const image = ref(null)
const title = ref(null)
const artist = ref(null)
const background = ref(null)
const currentTimeEl = ref(null)
const durationEl = ref(null)
const progress = ref(null)
const playerProgress = ref(null)
const prevBtn = ref(null)
const nextBtn = ref(null)
const playBtn = ref(null)
const songs = [
  {
    path: './assets/1.m4a',
    displayName: "سورة الانعام",
    cover: './assets/1.jpeg',
    artist: 'عمر بن عبد العزيز'
  },
  {
    path: './assets/2.m4a',
    displayName: 'سورة الفرقان',
    cover: './assets/2.jpg',
    artist: 'أحمد النفيس'
  },
  {
    path: './assets/3.mp3',
    displayName: 'سورة الزٌمر',
    cover: 'assets/3.jpg',
    artist: 'محمد عبادة الفردوس'
  }
]
const music = new Audio()

let musicIndex = 0
let isPlaying = false

function togglePlay() {
  if (isPlaying) {
    pauseMusic()
  } else {
    playMusic()
  }
}

function playMusic() {
  isPlaying = true
  //? Change play button icon
  if (playBtn.value) playBtn.value.classList.replace('fa-play', 'fa-pause')
  //? Set button hover title
  if (playBtn.value) playBtn.value.setAttribute('title', 'pause')
  music.play()
}

function pauseMusic() {
  isPlaying = false
  //? Change pause button icon
  playBtn.value.classList.replace('fa-pause', 'fa-play')
  //? Set button hover title
  playBtn.value.setAttribute('title', 'play')
  music.pause()
}

function loadMusic(song) {
  music.src = song.path
  if (title.value) title.value.textContent = song.displayName
  if (artist.value) artist.value.textContent = song.artist
  if (background.value) background.value.src = song.cover
  if (image.value) image.value.src = song.cover
}

function changeMusic(direction) {
  musicIndex = (musicIndex + direction + songs.length) % songs.length
  loadMusic(songs[musicIndex])
  playMusic()
}

function updateProgressBar() {
  const { duration, currentTime } = music
  const progressPercent = (currentTime / duration) * 100
  if (progress.value) progress.value.style.width = `${progressPercent}%`

  const formatTime = (time) => String(Math.floor(time)).padStart(2, '0')
  if (durationEl.value && duration) {
    durationEl.value.textContent = `${formatTime(duration / 60)}:${formatTime(duration % 60)}`
  } else {
    durationEl.value.textContent = '0:00'
  }
  if (currentTimeEl.value)
    currentTimeEl.value.textContent = `${formatTime(currentTime / 60)}:${formatTime(currentTime % 60)}`
}

function setProgressBar(e) {
  const width = playerProgress.value.clientWidth
  if (e) {
    const { offsetX } = e
    const durationX = music.duration
    const clickX = offsetX
    music.currentTime = (clickX / width) * durationX
  }
}
onMounted(() => {
  if (playBtn.value) playBtn.value.addEventListener('click', togglePlay)
  if (prevBtn.value) prevBtn.value.addEventListener('click', () => changeMusic(-1))
  if (nextBtn.value) nextBtn.value.addEventListener('click', () => changeMusic(1))
  if (music) music.addEventListener('ended', () => changeMusic(1))
  if (music) music.addEventListener('timeupdate', updateProgressBar)
  if (playerProgress.value) playerProgress.value.addEventListener('click', setProgressBar)
  loadMusic(songs[musicIndex])
})
</script>
<template>
  <div class="background fixed w-[200%] h-[200%] left-[-50%] top-[-50%] z-[-1]" id="bg-img">
    <img
      ref="background"
      class="absolute m-auto inset-0 min-w-[50%] min-h-[50%] blur-[15px] scale-[1.1]"
      src=""
      alt="background-img"
    />
  </div>

  <div class="container-section bg-[#e7e7e7] h-[420px] w-[300px] md:h-[520px] md:w-[400px] rounded-[20px]">
    <div class="player-img w-[200px] h-[200px] md:w-[300px] md:h-[300px] relative top-[-50px] left-[50px]">
      <img
        ref="image"
        src=""
        class="object-cover rounded-[20px] h-0 w-0 opacity-0 active"
        alt="player-img"
        id="cover"
      />
    </div>

    <h2 ref="title" id="title" class="text-[25px] text-center font-[500] mt-[10px] mr-0 mb-0"></h2>
    <h3 id="artist" ref="artist" class="text-[18px] text-center font-[500] mt-[10px]"></h3>

    <div
      ref="playerProgress"
      class="player-progress bg-[#ffff] rounded-[5px] h-[6px] cursor-pointer w-[90%]"
      id="player-progress"
    >
      <div
        ref="progress"
        id="progress"
        class="progress bg-[#212121] rounded-[5px] h-full w-[0%]"
      ></div>
      <div class="music-duration relative top-[-25px] flex justify-between">
        <span ref="currentTimeEl" id="current-time" class="current-time">0:00</span>
        <span ref="durationEl" id="duration" class="duration">0:00</span>
      </div>
    </div>
    <div class="player-controls relative top-[-15px] left-[120px] w-[200px]">
      <i ref="prevBtn" class="fa-solid w-[50px] relative left-[-25px] md:w-[62px] md:left-0 fa-backward" title="Previous" id="prev"></i>
      <i ref="playBtn" class="fa-solid w-[50px] relative left-[-25px] md:w-[62px] md:left-0 fa-play player-button" title="Play" id="play"></i>
      <i ref="nextBtn" class="fa-solid w-[50px] relative left-[-25px] md:w-[62px] md:left-0 fa-forward" title="Next" id="next"></i>
    </div>
  </div>
</template>

<style>
.container-section {
  box-shadow: 0 15px 30px rgb(0, 0, 0, 0.3);
  transition: all 0.5s ease;
}

.container-section:hover {
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.6);
}

.player-img img {
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.5);
}
.player-img:hover img {
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.8);
}

.player-img img.active {
  width: 100%;
  height: 100%;
  transition: all 0.5s;
  opacity: 1;
}

.player-progress {
  margin: 40px 20px 35px;
}
.progress {
  transition: width 0.1s linear;
}

.fa-solid {
  font-size: 30px;
  color: #666;
  cursor: pointer;
  user-select: none;
  transition: all 0.3s ease;
}

.fa-solid:hover {
  filter: brightness(40%);
}

.player-button {
  font-size: 44px;
  position: relative;
  top: 3px;
}
</style>
