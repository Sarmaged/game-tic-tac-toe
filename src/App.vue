<template>
  <div id="app">
    <div :class="['game-msg', { 'game-msg-lose': isMsgWin === false, 'game-msg-win': isMsgWin === true }]" v-if="msg">
      {{ msg }}
    </div>
    <div class="game">
      <div :class="['cell', { ai: dot === COMP, user: dot === USER }]" @click="move(x)" v-for="(dot, x) in dots" :keu="x">{{ dot }}</div>
    </div>
    <div class="game-actions" v-if="isStopGame">
      <a href="#" @click.prevent="reset">Reset</a>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',

  data: () => ({
    COMP: 'X',
  	USER: 'O',

    msg: '',
    isMsgWin: null,

    dots: new Array(9),

    isStopMove: false,
    isStopGame: false,

    win_array: ['012','345','678','036','147','258','048','246']
  }),

  created() {
    this.start()
  },

  methods: {
    start() {
      const rand = Math.floor(Math.random() * 9)
      if (rand > 3) {
        this.COMP = 'O'
      	this.USER = 'X'

      	this.dots[rand] = this.COMP
      }
    },
    getWinPos(i) {
      const pos = this.win_array[i]
      return {
        first: pos.substr(0,1),
    		second: pos.substr(1,1),
    		third: pos.substr(2,1)
      }
    },

    isUserWin(val) {
      for (let i = 0; i < 8; i++) {
        const { first, second, third } = this.getWinPos(i)

        if (this.dots[first] === val && this.dots[second] === val && this.dots[third] === val) {
          this.isStopMove = this.isStopGame = true
          this.msg = 'Вы выиграли!'
          this.isMsgWin = true
        }
      }
    },

    checkComp(val) {
      for (let i = 0; i < 8; i++) {
  			const { first, second, third } = this.getWinPos(i)

        if (this.dots[first] === val && this.dots[second] === val && !this.dots[third] && this.isStopMove === false) {
          this.dots[third] = val
          this.isStopMove = this.isStopGame = true
          this.msg = 'Вы проиграли!'
          this.isMsgWin = false
        }
        if (this.dots[first] === val && !this.dots[second] && this.dots[third] === val && this.isStopMove === false) {
          this.dots[second] = val
          this.isStopMove = this.isStopGame = true
          this.msg = 'Вы проиграли!'
          this.isMsgWin = false
        }
        if (!this.dots[first] && this.dots[second] === val && this.dots[third] === val && this.isStopMove === false) {
          this.dots[first] = val
          this.isStopMove = this.isStopGame = true
          this.msg = 'Вы проиграли!'
          this.isMsgWin = false
        }
  		}
  	},

    checkUser(val) {
      for (let i = 0; i < 8; i++) {
        const { first, second, third } = this.getWinPos(i)

        if (this.isStopMove === false) {
  			  if (this.dots[first] === val && this.dots[second] === val && !this.dots[third]) {
            this.dots[third] = this.COMP
            this.isStopMove = true
          }
        }

        if (this.isStopMove === false) {
  			  if (this.dots[first] === val && !this.dots[second] && this.dots[third] === val) {
            this.dots[second] = this.COMP
            this.isStopMove = true
          }
        }

        if (!this.dots[first] && this.dots[second] === val && this.dots[third] === val) {
          this.dots[first] = this.COMP
          this.isStopMove = true
        }

  			if (this.isStopMove) break
  		}
  	},

    move(index) {
      if (this.isStopGame) return
      if (this.dots[index]) return

      this.$set(this.dots, index, this.USER)

      if (Object.keys(this.dots).length === 9) {
        this.msg = 'Ничья'
        this.isMsgWin = null
        this.isStopGame = true
      }

      this.isUserWin(this.USER)
      this.checkComp(this.COMP)
      this.checkUser(this.USER)

      if (this.isStopMove === false) {
				for (let i = 0; i < 8; i++) {
          if (!this.dots[i]) {
            this.dots[i] = this.COMP
            break
          }
				}
			} else {
        this.isStopMove = false
      }
    },

    reset() {
      this.msg = ''
      this.isStopMove = false;
      this.isStopGame = false;
      this.dots = new Array(9)
      this.start()
    }
  }
}
</script>

<style>
body {
  margin: 0;
  box-sizing: border-box;

  background: rgb(2,0,36);
  background: linear-gradient(352deg, rgba(2,0,36,0.458420868347339) 0%, rgba(9,9,121,0.592874649859944) 35%, rgba(0,212,255,1) 100%);
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

  display: flex;
  align-items: center;
  justify-content: center;

  flex-direction: column;

  min-height: 100vh;
}
</style>

<style scoped>
.game {
  display: flex;
  align-items: center;
  /*justify-content: space-between;*/
  flex-wrap: wrap;

  width: 306px;
  height: 306px;

  background: #fff;

  margin: 25px 0;
}
.game-msg {
  background: #fff;
  padding: 5px 10px;
  height: 20px;
  border-radius: 5px;
}
.game-msg-lose {
  color: red;
}
.game-msg-win {
  color: green;
}
.game .cell {
  display: flex;
  align-items: center;
  justify-content: center;

  font-size: 32px;

  width: 100px;
  height: 100px;
  border: 1px solid rgba(0,0,0,0.3);
}
.game-actions a {
  color: #fff;
  padding: 5px 15px;
  background: rgba(9,9,121);
  text-decoration: none;

  height: 20px;

  text-transform: uppercase;

  border-radius: 20px;
}
</style>
