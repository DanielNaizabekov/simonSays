<template>
  <div class="game">
    <div class="game-tiles-wrap">
      <div
        v-for="tile in tiles"
        :key="tile.id"
        class="game-tile"
        :class="[tile.color, {active: tile.id === activeTileId}]"
        @click="onTileClick(tile.id)"
      />
    </div>

    <div class="game-controls" :class="{disabled: isGameStarted}">
      <h3>Round: {{round}}</h3>
      <button class="btn" @click="startRound">Start</button>

      <h5 v-show="losingRound">Losing round: {{ losingRound }}</h5>

      <h3 class="game-level-title">Game level:</h3>
      <div class="game-options">
        <input type="radio" id="low" value="low" v-model="level">
        <label for="low">Low</label>
        <br>
        <input type="radio" id="normal" value="normal" v-model="level">
        <label for="normal">Normal</label>
        <br>
        <input type="radio" id="high" value="high" v-model="level">
        <label for="high">High</label>
      </div>
    </div>
  </div>
</template>

<script>
import blueSound from '@/assets/sounds/1.mp3'
import redSound from '@/assets/sounds/2.mp3'
import yellowSound from '@/assets/sounds/3.mp3'
import greenSound from '@/assets/sounds/4.mp3'

const tiles = [
  { id: '1', color: 'blue', sound: blueSound },
  { id: '2', color: 'red', sound: redSound },
  { id: '3', color: 'yellow', sound: yellowSound },
  { id: '4', color: 'green', sound: greenSound },
]
const levels = [
  { name: 'low', interval: 1500 },
  { name: 'normal', interval: 1000 },
  { name: 'high', interval: 400 },
]
export default {
  data() {
    return {
      tiles,
      levels,
      round: 0,
      level: 'normal',
      activeTileId: null,
      randomTileIndexes: [],
      isTilesDisabled: true,
      isGameStarted: false,
      losingRound: '',
    }
  },
  computed: {
    currentTileOptions() {
      return this.tiles.find(tile => tile.id === this.activeTileId)
    },
    currentLevelOptions() {
      return this.levels.find(level => level.name === this.level)
    },
  },
  watch: {
    activeTileId(v) {
      if(!v) return

      const audio = new Audio(this.currentTileOptions.sound)
      audio.play()
      setTimeout(() => this.activeTileId = null, 300)
    },
  },
  methods: {
    getRandomTileIndexes() {
      let indexes = []
      for(let i = 0; i < this.round; i++) {
        indexes.push( Math.floor((Math.random() * this.tiles.length)) )
      }
      return indexes
    },
    highlightTiles() {
      this.isTilesDisabled = true
      let randomTileIndexes = [...this.randomTileIndexes]
      const { interval } = this.currentLevelOptions

      function highlightTilesQueue() {
        if(!randomTileIndexes.length) return this.isTilesDisabled = false

        const activeTileIndex = randomTileIndexes.splice(0, 1)
        this.activeTileId = this.tiles[activeTileIndex].id
        setTimeout(highlightTilesQueue.bind(this), interval)
      }
      highlightTilesQueue.call(this)
    },
    startRound() {
      this.isGameStarted = true
      this.losingRound = ''
      this.round++
      this.randomTileIndexes = this.getRandomTileIndexes()
      this.highlightTiles()
    },
    onTileClick(id) {
      if(this.isTilesDisabled) return

      const outmostTileIndex = this.randomTileIndexes.splice(0, 1)
      if(id !== this.tiles[outmostTileIndex].id) {
        alert('You lost')
        this.reset()
      } else if(!this.randomTileIndexes.length) {
        this.activeTileId = id
        setTimeout(() => this.startRound(), 500)
      } else {
        this.activeTileId = id
      }
    },
    reset() {
      this.losingRound = this.round
      this.round = 0
      this.level = 'normal'
      this.randomTileIndexes = []
      this.isTilesDisabled = true
      this.isGameStarted = false
    },
  },
}
</script>

<style lang="sass">
.game
  display: flex
  justify-content: center
  align-items: center

.game-tiles-wrap
  width: 300px
  height: 300px
  display: flex
  flex-wrap: wrap
  border-radius: 50%
  overflow: hidden
  cursor: pointer

.game-tile
  width: 50%
  height: 50%
  opacity: .5
  &:hover
    border-color: #000
  &.active
    opacity: 1
.blue
  background: #1E90FF
  border-top: 2px solid transparent
  border-left: 2px solid transparent
  border-radius: 100% 0 0 0
.red
  background: #FF5643
  border-top: 2px solid transparent
  border-right: 2px solid transparent
  border-radius: 0 100% 0 0
.yellow
  background: #FEEF33
  border-bottom: 2px solid transparent
  border-left: 2px solid transparent
  border-radius: 0 0 0 100%
.green
  background: #BEDE15
  border-bottom: 2px solid transparent
  border-right: 2px solid transparent
  border-radius: 0 0 100% 0

.game-controls
  margin-left: 50px
  &.disabled
    pointer-events: none

.game-level-title
  margin: 30px 0 5px 0
</style>