<template>
  <div id="app" class="container">
    <!-- SKILL BAR -->
            <section class="flex-row my-5">
                <div v-if="!is_attack" style="min-height:100vh;" class="col-12 text-center d-flex justify-content-center align-items-center flex-column">
                    <img style="width:200px" src="./assets/zombie.webp"/>
                    <h2 class="display-4 fw-bolder">OMG! THERE IS A ZOMBIE ON THE ROAD!</h2>
                    <button @click="start_game" class="btn bg-gradient btn-lg w-50 p-3 btn-danger">DEFEAT IT!!</button>
                    <p class="mt-4 text-secondary">Mini game by <a href="https://github.com/BoraKucukkara/zombiehunt">Bora Kucukkara</a></p>
                </div>
                <div v-if="is_attack" class="col m-auto text-center">
                    <!-- HEALTH BAR -->
                    <section class="row py-5">
                        <div class="col-6 player">
                            <h4>You</h4>
                            <img v-if="player_heal > 0" src="./assets/player.webp">
                            <img v-else class="became_zombie" src="./assets/zombie.webp"/>
                            <div class="healthbarbox">
                                <div class="healthbar" :style="{width : player_heal + '%'}"> {{ player_heal }}% </div>
                            </div>
                        </div>
                        <div class="col-6 zombie">
                            <h4>Zombie</h4>
                            <img v-if="zombie_heal > 0" class="zombie-img" src="./assets/zombie.webp"/>
                            <img v-else class="zombie_down" src="./assets/zombie.webp"/>
                            <div class="healthbarbox">
                                <div class="healthbar" :style="{width : zombie_heal + '%'}"> {{ zombie_heal }}% </div>
                            </div>
                        </div>
                    </section>
                    <div v-if="player_heal <= 0 && zombie_heal > 0">
                        <h1 class="lost text-danger fw-bolder display-1">You became a zombie!</h1>
                        <button @click="runaway" class="btn btn-danger btn-lg">Retry?</button>
                    </div>
                    <div v-else-if="zombie_heal <= 0 && player_heal > 0">
                        <h1 class="won text-success fw-bolder display-1">YOU WON!</h1>
                        <button @click="runaway" class="btn bg-gradient btn-success btn-lg">Beat Another Zombie</button>
                    </div>
                    <div v-else-if="zombie_heal === 0 && player_heal === 0">
                        <h1 class="won text-secondary fw-bolder display-3">You have been bitten, welcome to the club!</h1>
                        <button @click="runaway" class="btn bg-gradient btn-success btn-lg">Beat Another Zombie</button>
                    </div>
                    <div v-else class="py-3">
                        <button @click="attack" class="btn-skill btn btn-danger btn-lg">Attack</button>
                        <button @click="heavy_attack" class="btn-skill btn btn-lg btn-primary" :disabled="super_attack_counter <= 0">Super Attack <span class="badge bg-dark bg-opacity-25">{{ super_attack_counter }}</span></button>
                        <button @click="healme" class="btn-skill btn btn-lg btn-success" :disabled="heal_up_counter <= 0">Heal Yourself<span class="badge bg-dark bg-opacity-25">{{ heal_up_counter }}</span></button>
                    </div>
                </div>
            </section>
            <!-- LOG -->
            <section v-if="is_attack" class="p-3">
                <ul class="">
                    <li class=""
                        :class="{'turn_player' : log.turn == 'YOU', 'turn_zombie' : log.turn == 'ZOMBIE' }"
                        v-for="log in logs" :key="log">{{ log.turn }} {{ log.type }} <span class="rounded-pill">{{ log.point }}
                        <i class="bg-light text-danger rounded-pill px-1" v-show="log.point > 20 && log.type == 'SUPER ATTACKED'">Critical Hit!</i>
                        <i class="bg-light text-success rounded-pill px-1" v-show="log.point > 25">Critical Heal +</i>
                    </span></li>
                </ul>
            </section>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      player_heal : 100,
      zombie_heal : 100,
      super_attack_counter : 3,
      heal_up_counter: 3,
      is_attack : false,
      logs : []
    }
  },
  methods: {
        start_game() {
            this.is_attack = true
        },
        attack() {
            var damage = Math.ceil(Math.random()* 10);
            this.zombie_heal -= damage;
            this.zombie_attack();
            this.add_to_log({turn : "YOU", type : "ATTACKED", point : + damage});

        },
        heavy_attack() {
            var damage = Math.ceil(Math.random()* 15) + 10;
            this.zombie_heal -= damage;
            this.zombie_attack();
            this.add_to_log({turn : "YOU", type : "SUPER ATTACKED", point : + damage});
            this.super_attack_counter -= 1
        },
        zombie_attack() {
            var that = this
            setTimeout(function() {
                var damage = Math.ceil(Math.random()* 20);
                that.player_heal -= damage;
                that.add_to_log({turn : "ZOMBIE", type : "ATTACKED", point : + damage});
            }, 100)

        },
        healme() {
            var heal = Math.ceil(Math.random() * 20) + 10;
            this.player_heal += heal;
            this.zombie_attack();
            this.add_to_log({turn : "YOU", type : "HEALED YOURSELF", point : + heal});
            this.heal_up_counter -= 1
        },
        runaway() {
            this.is_attack = false
            this.player_heal = 100
            this.zombie_heal = 100
            this.super_attack_counter = 3
            this.heal_up_counter = 3
            this.logs = []
        },
        add_to_log(log) {
            this.logs.push(log);
        }
    },
    watch: {
        player_heal : function(value){
            if(value <= 0) {
                this.player_heal = 0;
            } else if (value >= 100) {
                this.player_heal = 100;
            }
        },
        zombie_heal : function(value){
            if(value <= 0) {
                this.zombie_heal = 0;
            }
        }
    }
}
</script>

<style>
@import url(main.css);
#app {
  min-height: 100vh;
}
</style>
