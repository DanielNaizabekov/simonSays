<template>
  <div id="app">
    <h1 class="title">Simon Says</h1>
    <Game />
  </div>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'App',
  components: {
    Game,
  },
}
</script>

<style lang="sass">
#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  color: #333333

*
  box-sizing: border-box

.title
  text-align: center

.btn
  background: #6DABE8
  border-radius: 10px
  border: 0
  outline: none
  font-size: 22px
  padding: 7px 25px
  cursor: pointer
  &:hover
    background: #78BCFF
</style>