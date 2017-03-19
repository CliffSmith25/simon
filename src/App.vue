<template>
  <div id="app">
    <div id="game">
      <div id="top-row">
        <button @mousedown="toneButtonPushed(0)" id="but-0" class="tone-button"></button>
        <button @mousedown="toneButtonPushed(1)" id="but-1" class="tone-button"></button>
      </div>
      <div id="middle-row">
        

      </div>
      <div id="bottom-row">
        <button @mousedown="toneButtonPushed(2)" id="but-2" class="tone-button"></button>
        <button @mousedown="toneButtonPushed(3)" id="but-3" class="tone-button"></button>
      </div>
    </div>
    <audio ref="simonSound0" src="/static/simonSound0.mp3"></audio>
    <audio ref="simonSound1" src="/static/simonSound1.mp3"></audio>
    <audio ref="simonSound2" src="/static/simonSound2.mp3"></audio>
    <audio ref="simonSound3" src="/static/simonSound3.mp3"></audio>
    <audio ref="wrong" src="/static/wrong.mp3"></audio>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: function () {
    return {
      compTones: [],
      playerTones: [],
      playerTone: 0,
      compTurn: true,
      intervalID: '',
      onLoad: this.initGame()
    }
  },
  methods: {
    toneButtonPushed: function (buttonPushed) {
      const toneName = 'simonSound' + buttonPushed.toString()
      this.$refs[toneName].play()
    },
    initGame: function () {
      this.compTones = []
      this.playerTones = []
      this.playerTurn = 0
      this.compTurn = true
      this.addTone()
      this.showTones()
    },
    showTones: function () {
      let gen = this.getNextTone()
      this.intervalID = setInterval(this.showTone(gen.next()), 1000)
    },
    showTone: function (tone) {
      console.log(tone)
      const toneName = 'simonSound' + tone.value.toString()
      console.log(toneName)
      this.$refs[toneName].play()
    },
    addTone: function () {
      this.compTones.push(Math.floor(Math.random() * 3))
    },
    getNextTone: function* () {
      let i = 0
      for (i; i < this.compTones.length - 1; i++) {
        yield this.compTones[i]
      }
      return this.compTones[i]
    },
    stopTones: function () {
      clearInterval(this.intervalID)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#but-0 {
  background-color: red;
}

#but-1 {
  background-color: blue;
}

#but-2 {
  background-color: green;
}

#but-3 {
  background-color: yellow;
}

.tone-button {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  border: none;
  box-shadow: 5px 5px 10px black;
  margin: 20px;
  outline: none;
}

.tone-button:active {
  box-shadow: 1px 1px 1px black;
}
</style>
