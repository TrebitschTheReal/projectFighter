<template>
  <div id="app">
    <div class="container-fluid bg-dark p-5">
      <div class="m-2 jumbotron">
        <div class="mb-3">
          <div class="row">
            <div class="col progress">
              <div class="mx-auto progress-bar bg-success" :class="player.healthBarColor" :style="reanimatePlayersHealthBar()"> {{player.actualHealth}} </div>
            </div>
            <div class="col progress">
              <div class="mx-auto progress-bar bg-success" :class="enemy.healthBarColor" :style="reanimateEnemiesHealthBar()"> {{enemy.actualHealth}} </div> <!-- class="border-dark border border-1" -->
            </div>
          </div>
        </div>
        <div class="mx-auto text-center">
          <div class="row py-2">
            <div class="col">
              <img :class="{dmgTakenImgShake : player.dmgTaken}" src="./player_psyduck.png" width="200" height="250">
            </div>
            <div class="col">
              <img :class="{dmgTakenImgShake : enemy.dmgTaken}" src="./enemy_artorias.png" width="200" height="250">
            </div>
          </div>
          <div class="row my-2">
            <div class="col text-uppercase mx-auto h5">{{ player.name }}</div>
            <div class="col text-uppercase mx-auto h5">{{ enemy.name }}</div>
          </div>

        </div>
        <div class="m-2 jumbotron">
          <div class="mx-auto text-center">
            <div class="">
              <template v-if="gameMechanics.gameStarted === null" class="row">
                <div class="btn btn-primary col-3 text-uppercase mx-auto h4" @click="startNewGame()">start new game</div>
              </template>
              <template v-else-if="gameMechanics.gameStarted">
                <template class="row mx-auto text-center">
                  <div class="btn btn-danger col-2 text-uppercase mx-3 h4" @click="normalAttackByPlayer()">Attack</div>
                  <div class="btn btn-warning col-2 text-uppercase mx-3 h4" @click="SpecialAttackByPlayer()">Special Attack</div>
                  <div class="btn btn-info col-2 text-uppercase mx-3 h4" @click="healPlayer()">Heal</div>
                  <div class="btn btn-dark col-2 text-uppercase mx-3 h4" @click="giveUp()">Give up</div>
                </template>
                  <div class="border border-dark rounded bg-light my-3">
                      <div class="my-2">
                          <span class="text-dark h5">Battle Log</span>
                          <div class="row">
                              <ul class="list-group col">
                                  <li  class="mx-auto h4 text-success list-group-item" v-for="(item, i) in player.dmgCaused">{{ player.name }} caused {{ player.dmgCaused[i] }} dmg to {{ enemy.name }}</li>
                                  <!-- <li  class="mx-auto h4 text-danger list-group-item" v-for="(item, i) in player.dmgRecieved">{{ player.name }} received {{ enemy.dmgCaused[i] }} dmg from {{ enemy.name }}</li> -->
                              </ul>
                              <ul class="list-group col">
                                  <!-- <li  class="mx-auto h4 text-success list-group-item" v-for="(item, i) in enemy.dmgRecieved">{{ enemy.name }} received {{ player.dmgCaused[i] }} dmg from {{ player.name }}</li> -->
                                 <li  class="mx-auto h4 text-danger list-group-item" v-for="(item, i) in enemy.dmgCaused">{{ enemy.name }} caused {{ enemy.dmgCaused[i] }} dmg to {{ player.name }}</li>
                              </ul>
                          </div>
                      </div>
                </div>
              </template>
              <template v-else="" class="">
                <div class="mx-auto text-center h2 alert alert-success border px-5">{{ theWinner() }} won!</div>
                <div class="btn btn-primary mx-auto text-center h2 border px-5" @click="endMatch()">Exit</div>
              </template>
            </div>
          </div>
        </div>
     </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: function () {
    return {

      testVariable: 30,
      maxHealth: 100,

      gameMechanics: {
          gameStarted: null,
          someOneWon: false,
      },

      player: {
          healthBarColor: 'bg-success',
          actualHealth: 0,
          dmgTaken: false,
          name: 'Psyduck',
          dmgRecieved: [],
          dmgCaused: [],
      },

      enemy: {
          healthBarColor: 'bg-succes',
          actualHealth: 0,
          dmgTaken: false,
          name: 'Sir Artorias',
          dmgRecieved: [],
          dmgCaused: [],
      },
    }
  },

  computed: {

  },

  methods: {
    reanimateEnemiesHealthBar: function(){
      this.enemy.actualHealth <= 30 ? this.enemy.healthBarColor = 'bg-danger': this.enemy.healthBarColor = 'bg-success';
      return{
        width: this.enemy.actualHealth + '%',
      }
    },
    reanimatePlayersHealthBar: function () {
      this.player.actualHealth <= 30 ? this.player.healthBarColor = 'bg-danger': this.player.healthBarColor = 'bg-success';
      return{
        width: this.player.actualHealth + '%',
      }
    },
    startNewGame: function(){
      this.gameMechanics.someOneWon = false;
      this.gameMechanics.gameStarted = !this.gameMechanics.gameStarted;
      this.player.actualHealth = 100;
      this.enemy.actualHealth = 100;
    },
    normalAttackByPlayer: function(){
      let vm = this;
      vm.player.dmgCaused.push(Math.floor(Math.random() * 10 + 1));
      vm.enemy.actualHealth -= vm.player.dmgCaused[(vm.player.dmgCaused.length) -1];
      vm.enemy.dmgRecieved.push(vm.player.dmgCaused[(vm.player.dmgCaused.length) -1]);
      this.roundChecker();
      vm.enemy.dmgTaken = true;
      setTimeout(function(){
        vm.enemy.dmgTaken = false;
        vm.normalAttackByEnemy();
      }, 300);
    },
    normalAttackByEnemy: function(){
      let vm = this;
        vm.enemy.dmgCaused.push(Math.floor(Math.random() * 10 + 1));
        vm.player.actualHealth -= vm.enemy.dmgCaused[(vm.enemy.dmgCaused.length) -1];
        vm.player.dmgRecieved.push(vm.enemy.dmgCaused[(vm.enemy.dmgCaused.length) -1]);
      this.roundChecker();
      vm.player.dmgTaken = true;
      setTimeout(function(){
        vm.player.dmgTaken = false;
      }, 300);
    },
    SpecialAttackByPlayer: function(){
        let vm = this;
        vm.player.dmgCaused.push(Math.floor(Math.random() * 20 + 5));
        vm.enemy.actualHealth -= vm.player.dmgCaused[(vm.player.dmgCaused.length) -1];
        vm.enemy.dmgRecieved.push(vm.player.dmgCaused[(vm.player.dmgCaused.length) -1]);
        this.roundChecker();
        vm.enemy.dmgTaken = true;
        setTimeout(function(){
            vm.enemy.dmgTaken = false;
            vm.normalAttackByEnemy();
        }, 300);
    },
    roundChecker: function(){
      if(this.player.actualHealth <= 0){
        this.player.actualHealth = 0;
        this.gameMechanics.someOneWon = !this.gameMechanics.someOneWon;
        this.gameMechanics.gameStarted = false;
      }
      else if (this.enemy.actualHealth <= 0) {
        this.enemy.actualHealth = 0;
        this.gameMechanics.someOneWon = !this.gameMechanics.someOneWon;
        this.gameMechanics.gameStarted = false;
      }
    },
    theWinner: function(){
      if(this.player.actualHealth <= 0){
        return this.enemy.name;
      }
      else{
        return this.player.name;
      }
    },
    giveUp: function(){
      this.player.actualHealth -= this.player.actualHealth;
      this.roundChecker();
    },
    endMatch: function(){
      this.player.actualHealth = 0;
      this.player.dmgCaused  = [];
      this.player.dmgRecieved = [];
      this.enemy.actualHealth = 0;
      this.enemy.dmgCaused  = [];
      this.enemy.dmgRecieved = [];

      this.gameMechanics.gameStarted = null;
    },

    healPlayer: function(){
      if(this.player.actualHealth < this.maxHealth)
      this.player.actualHealth += Math.floor(Math.random() * 9 + 1);
      if(this.player.actualHealth > this.maxHealth){
        this.player.actualHealth = this.maxHealth;
      }
      let vm = this;
        setTimeout(function(){
            vm.enemy.dmgTaken = false;
            vm.normalAttackByEnemy();
        }, 300);
    },

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

.dmgTakenImgShake {
  animation: shake 0.5s;
  animation-iteration-count: infinite;
}

@keyframes shake {
  0% { transform: translate(1px, 1px) rotate(0deg); }
  10% { transform: translate(-1px, -2px) rotate(-1deg); }
  20% { transform: translate(-3px, 0px) rotate(1deg); }
  30% { transform: translate(3px, 2px) rotate(0deg); }
  40% { transform: translate(1px, -1px) rotate(1deg); }
  50% { transform: translate(-1px, 2px) rotate(-1deg); }
  60% { transform: translate(-3px, 1px) rotate(0deg); }
  70% { transform: translate(3px, 1px) rotate(-1deg); }
  80% { transform: translate(-1px, -1px) rotate(1deg); }
  90% { transform: translate(1px, 2px) rotate(0deg); }
  100% { transform: translate(1px, -2px) rotate(-1deg); }
}

</style>