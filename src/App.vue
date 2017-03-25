<template>
  <div id="app">
    <div id="game">
      <div id="top-row">
        <i class="fa fa-arrow-left but-0" v-bind:class="{ active: pushed0 }" aria-hidden="true" @mousedown="toneButtonPushed(0)"></i>
        <i class="fa fa-arrow-left but-1" v-bind:class="{ active: pushed1 }" aria-hidden="true" @mousedown="toneButtonPushed(1)"></i>
      </div>
      <div id="middle-row">
          <button class="level" v-on:click="initGame">{{ compTones.length }}</button>
          <button class="strictButton" v-on:click="toggleStrict" v-bind:class="{ strict: strict }">{{ strictLabel }}</button>
      </div>
      <div id="bottom-row">
        <i class="fa fa-arrow-left but-2" v-bind:class="{ active: pushed2 }" aria-hidden="true" @mousedown="toneButtonPushed(2)"></i>
        <i class="fa fa-arrow-left but-3" v-bind:class="{ active: pushed3 }" aria-hidden="true" @mousedown="toneButtonPushed(3)"></i>
      </div>
      <h1>{{ msg }}</h1>
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
      strict: false,
      winLevel: 20,
      pushed0: false,
      pushed1: false,
      pushed2: false,
      pushed3: false,
      msg: ''
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
              this.msg = 'YOU WIN!!!!'
              setTimeout(() => {
                this.initGame()
              }, 2500)
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
      this.msg = ''
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
      this['pushed' + tone.value.toString()] = true
      this.$refs[toneName].play()
      setTimeout(() => {
        this['pushed' + tone.value.toString()] = false
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

#bottom-row {
  font-size: 140px;
}

button {
  border: none;
  border-radius: 50%;
  width: 70px;
  height: 70px;
  margin-left: 10px;
  margin-right: 10px;
  margin-top: 2px;
  font-size: 1.25em;
}

button:hover {
  cursor: pointer;
}

button:focus {
  outline: none;
}

button:active {
  filter: brightness(50%);
}

.but-0 {
  transform: rotate(90deg);
  position: absolute;
  left: 0px;
  top: 0px;
  color: #A8A429;
}

.but-0.active {
  color: #FFF700 !important;
}

.but-0:active {
  color: #FFF700;
}

.but-1 {
  transform: rotate(180deg);
  position: absolute;
  left: 190px;
  top: 0px;
  color: #B23D2B;
}

.but-1:active {
  color: #E81F00;
}

.but-1.active {
  color: #E81F00 !important;
}

.but-2 {
  transform: rotate(0deg);
  position: absolute;
  left: 0px;
  top: 190px;
  color: #222B8C;
}

.but-2:active {
  color: #0015FF;
}

.but-2.active {
  color: #0015FF !important;
}

.but-3 {
  transform: rotate(270deg);
  position: absolute;
  left: 180px;
  top: 200px;
  color: #1C9420;
}

.but-3:active {
  color: #0CE813;
}

.but-3.active {
  color: #0CE813 !important;
}

#game {
  width: 320px;
  text-align: center;
  height: 320px;
  position: relative;
}

h1 {
  text-align: center;
  position: absolute;
  top: 320px;
  left: 70px;
}

i:hover {
  cursor: pointer;
}

.level {
  background-color: white;
  font-size: 3em;
}

#middle-row {
  border-width: medium;
  border-style: solid;
  border-radius: 50%;
  width: 150px;
  height: 150px;
  position: absolute;
  left: 80px;
  top: 90px;
}

.strictButton {
  background-color: #0CE813;
}

.strictButton.strict {
  background-color: #E81F00;
}

#top-row {
  font-size: 140px;
}

</style>
