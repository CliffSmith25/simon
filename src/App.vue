<template>
  <div id="app">
    <div id="game">
      <div id="top-row">
        <button @mousedown="toneButtonPushed(0)" v-bind:class="{ pushed: but0Pushed }" id="but-0" class="tone-button"></button>
        <button @mousedown="toneButtonPushed(1)" v-bind:class="{ pushed: but1Pushed }" id="but-1" class="tone-button"></button>
      </div>
      <div id="middle-row">
        <p>{{ compTones.length }}</p>
        <button v-on:click="initGame">Reset</button>
        <p>{{ strictLabel }}</p>
        <button v-on:click="toggleStrict">Strict</button>
      </div>
      <div id="bottom-row">
        <button @mousedown="toneButtonPushed(2)" v-bind:class="{ pushed: but2Pushed }" id="but-2" class="tone-button"></button>
        <button @mousedown="toneButtonPushed(3)" v-bind:class="{ pushed: but3Pushed }" id="but-3" class="tone-button"></button>
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
      playerTone: 0,
      compTurn: true,
      intervalID: '',
      but0Pushed: false,
      but1Pushed: false,
      but2Pushed: false,
      but3Pushed: false,
      strict: false,
      winLevel: 3
    }
  },
  computed: {
    strictLabel: function () {
      return this.strict ? 'Strict' : 'Easy'
    }
  },
  mounted: function () {
    setTimeout(this.initGame, 0)
  },
  methods: {
    toneButtonPushed: function (buttonPushed) {
      if (this.compTurn === false) {
        if (buttonPushed === this.compTones[this.playerTone]) {
          const toneName = 'simonSound' + buttonPushed.toString()
          this.$refs[toneName].play()
          if (this.playerTone === this.compTones.length - 1) {
            if (this.compTones.length === this.winLevel) {
              alert('YOU WIN!')
              this.initGame()
            } else this.toggleTurn()
          } else {
            this.playerTone++
          }
        } else {
          if (this.strict === true) {
            this.$refs.wrong.play()
            this.initGame()
          } else {
            this.$refs.wrong.play()
            this.compTurn = true
            this.showTones()
          }
        }
      } else {
        this.$refs.wrong.play()
      }
    },
    toggleStrict: function () {
      this.strict = !this.strict
      this.initGame()
    },
    initGame: function () {
      this.compTones = []
      this.playerTurn = 0
      this.compTurn = true
      this.addTone()
      this.showTones()
    },
    showTones: function () {
      let gen = this.getNextTone()
      this.intervalID = setInterval(() => {
        this.showTone(gen.next())
      }, 1000)
    },
    showTone: function (tone) {
      const toneName = 'simonSound' + tone.value.toString()
      this.toneToggle(tone.value)
      this.$refs[toneName].play()
      setTimeout(() => {
        this.toneToggle(tone.value)
      }, 500)
      if (tone.done === true) {
        this.stopTones()
        this.toggleTurn()
      }
    },
    toggleTurn: function () {
      if (this.compTurn === false) {
        this.compTurn = true
        this.addTone()
        this.showTones()
      } else {
        this.compTurn = false
        this.playerTone = 0
      }
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
    },
    toneToggle: function (tone) {
      const butProp = 'but' + tone + 'Pushed'
      this[butProp] ? this[butProp] = false : this[butProp] = true
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
  box-shadow: 10px 10px 20px black;
  margin: 20px;
  outline: none;
}

.pushed {
  box-shadow: 3px 3px 8px black;
}

.tone-button:active {
  box-shadow: 3px 3px 8px black;
}
</style>
