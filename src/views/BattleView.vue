<template>
    <div>
        <div class="battle_cont"> 
            <div class="pers_cont">
                <div>
                    <div class="pers_stat_cont">
                        <div class="stats">
                            <div class="icon hp_icon"></div>
                            {{ players[0].hp }}
                        </div>
                        <div class="stats">
                            <div class="icon attack_icon"></div>
                            {{ players[0].attack }}
                        </div>
                    </div>
                    <div :class="'pers_img '+ players[0].animation">
                    </div>
                </div>
                <div>
                    <div class="pers_stat_cont">
                        <div class="stats">
                            <div class="icon hp_icon"></div>
                            {{ players[1].hp }}
                        </div>
                        <div class="stats">
                            <div class="icon attack_icon"></div>
                            {{ players[1].attack }}
                        </div>
                    </div>
                    <div :class="'pers_img '+ players[1].animation">
                    </div>
                </div>
            </div>
            <div class="button_cont">
                <div @click="BattleFunc(0)" class="icon attack_icon">
                </div>
                <div @click="BattleFunc(1)" class="def_icon icon">
                </div>
            </div>
        </div>
        
        <!--здесь игрок\игроки выбирают количество характеристик перед началом сражения-->
        <div class="modal_params" v-if="counterPlayer < 2">
            <!--если игрок сражается против игрока-->
            <div class="modal_container" v-if="this.enemyNow != false">
                <div v-if="counterPlayer == 0">
                    <h2>Выберите Параметры для первого игрока</h2>
                    <div class="input_cont">
                        Здоровье: <input type="number" v-model="players[0].hp" placeholder="здоровье" class="input_params">
                    </div>
                    <div class="input_cont">
                        Атака: <input type="number" v-model="players[0].attack" placeholder="здоровье" class="input_params">
                    </div>
                    <button @click="counterPlayer++">Далее</button>
                    <button @click="counterPlayer--">Назад</button>
                </div>
                <div v-if="counterPlayer == 1">
                    <h2>Выберите Параметры для второго игрока</h2>
                    <div class="input_cont">
                        Здоровье: <input type="number" v-model="players[1].hp" placeholder="здоровье" class="input_params">
                    </div>
                    <div class="input_cont">
                        Атака: <input type="number" v-model="players[1].attack" placeholder="атака" class="input_params">
                    </div>
                    <button @click="counterPlayer++">Начать</button>
                    <button @click="counterPlayer--">Назад</button>
                </div>            
            </div>
        </div>
    </div>
</template>

<script>
import '../styles/pers_animation.css';
import '../styles/icons.css'

export default {
    name: "BattleView",
    data() {
        return{
            //проверка того против кого будет идти сражение, против игрока или против ИИ
            enemyNow: true,
            //данные игроков
            players: [
                {
                    //здоровье
                    hp: 0,
                    //атака
                    attack: 0,
                    //текущая анимация статика\атака\защита\спав\смерть
                    animation: "pers-static-0"
                },
                {
                    hp: 0,
                    attack: 0,
                    animation: "pers-static-1"
                }
            ],
            //общее кол-во очков, для распределения, перед боем
            paramsPlayers: 0,
            //следит за тем, кто из игроков выбирает и идёт ли сейчас сражение, если нет
            counterPlayer: 0,
            //проверка того кто сейчас ходит первый(true) или второй игрок(false)
            combatRound: true
        }
    },
    created() {
    //проверяем с кем идёт сражение, с игроком другим, или с человеком
        if(this.$route.params.enemy_is == "computer") {
            this.enemyNow = false
        }
    },
    methods: {
        BattleFunc(playerDo) {
            //атака
            if(playerDo == 0) {
                //проверка чей ход
                if(this.combatRound == true) {
                    this.AttackPlayers(0)
                } else {
                    this.AttackPlayers(1)
                }
            //защина
            } else {
                if(this.combatRound == true) {
                    this.DefPers(0)
                } else {
                    this.DefPers(1)
                }
            }
            this.combatRound = !this.combatRound
        },
        AttackPlayers: function(player) {
            if(player == 0) {
                //первый игрок
                this.players[1].hp = this.players[1].hp - this.players[0].attack
                this.AllnimationsPers("attack", 0);
                //анимация получения урона
                this.ifDie(1);
                
            } else {
                //второй игрок
                this.players[0].hp = this.players[0].hp - this.players[1].attack
                this.AllnimationsPers("attack", 1);
                //анимация получения урона
                this.ifDie(0);
            }
            //анимация того кто атакует
        },
        DefPers: function(player) {
            if(player == 0) {
                //первый игрок
                this.players[0].hp = this.players[0].hp + 5
                this.AllnimationsPers("defense", 0);
            } else {
                //второй игрок
                this.players[1].hp = this.players[1].hp + 5
                this.AllnimationsPers("defense", 1);
            }
        },
        //общая функция для всех анимаций, чтобы можно было не переписывать один и тот же код много раз.
        AllnimationsPers: function(status, id_player) {
            // status - то какую анимацию нужно будет отобразить
            //id_player - на каком из персонажей 0 или 1
            switch(status) {
                //атака
                case "attack": 
                    this.players[id_player].animation = 'pers-attack-' + id_player;
                    setTimeout(() => {
                    this.players[id_player].animation = 'pers-static-'+ id_player;
                    }, 1900);
                break;
                //удар приняла защита
                case "block":
                this.players[id_player].animation = 'pers-block-'+ id_player
                    setTimeout(() => {
                        this.players[id_player].animation = 'pers-static-' + id_player;
                    }, 900);
                    break;
                //получение урона
                case "taking_dmg":
                    this.players[id_player].animation = 'pers-taking_dmg-' + id_player;
                    setTimeout(() => {
                    this.players[id_player].animation = 'pers-static-'+ id_player;
                    }, 2100);
                    break;
                //ход защиты от персонажа +5 здоровья
                case "defense":
                    this.players[id_player].animation = 'pers-def-' + id_player;
                    setTimeout(() => {
                    this.players[id_player].animation = 'pers-static-'+ id_player;
                    }, 2000);
                    break;
                //смерть игрока
                case "death": 
                    this.players[id_player].animation = 'pers-death-anim-' + id_player;
                    setTimeout(() => {
                    this.players[id_player].animation = 'pers-death-img-'+ id_player;
                    this.WinMessage();
                    }, 2000);
                    break;
            }
        },
        //проверка смерти
        ifDie: function(player) {
            if(player == 0) {
                if(this.players[0].hp <= 0) {
                    this.AllnimationsPers("death", 0);
                } else {
                    this.AllnimationsPers("taking_dmg", 0);
                }
            } else {
                if(this.players[1].hp <= 0) {
                    this.AllnimationsPers("death", 1);
                } else {
                    this.AllnimationsPers("taking_dmg", 1);
                }
            }
        },
        WinMessage: function() {
            if(confirm('ПОЗДРАВЛЯЮ С ПОБЕДОЙ!!')) {
                this.$router.push('/');
            }
        }
    }
}
</script>

<style scoped>
.modal_params{
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: black;
    display: flex;
    justify-content: center;
    align-items: center;
}
.modal_container{
    width: 40%;
    height: 60%;
    background-color: white;
    color: black;
    padding-left: 3%;
}
.pers_img{
    min-height: 500px;
    max-height: 700px;
    min-width: 300px;
    max-width: 500px;
    background-position: bottom;
    background-repeat: no-repeat;
    background-size: contain;
}
.pers_cont{
    padding-top: 2%;
    display: flex;
    justify-content: center;
}
.battle_cont{
    background-image: url('../assets/arena2.jpg');
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;
    max-width: 100%;
    min-height: 100vh;
}
.button_cont{
    display: flex;
    justify-content: center;
}
.pers_stat_cont{
    display: flex;
    justify-content: center;
    padding: 1%;
}
.stats{
    margin: 1%;
    color: white;
    font-size: 120%;
    display: flex;
    align-items: center;
}
.input_cont{
    margin: 1%;
    text-align: left;
}
.input_params{
    padding: 1%;
    font-size: 110%;
}
</style>