<template>
  <div class="main_container">
    <div class="menu_container">
        <h2 class="arena_header">Arena</h2>
        <!--Основное меню-->
        <div class="menu_headers" v-if="isMune">
          <p>
            <span class="menu_header" @click="isMune = !isMune">Начать</span>
          </p>
          <p>
            <router-link to="/about" class="menu_header">История матчей</router-link>
          </p>
        </div>
        <!--меню для выбора с кем сражение идёт, с игроком или ИИ-->
        <div class="menu_headers" v-else>
          <p>
            <router-link :to="{ name: 'BattleView', params: { 'enemy_is': 'player'} }" class="menu_header" @click="stopPlayback()">Игра против другого игрока</router-link>
          </p>
          <p @click="isMune = !isMune" class="menu_header">
            Назад
          </p>
        </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'HomeView',
  data() {
    return{
      isMune: true,
      audio: ''
    }
  },
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Uncial+Antiqua&display=swap');
.main_container{
  background-image: url('../assets/arena1.jpg');
  background-repeat: no-repeat;
  background-size: cover;
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.menu_container{
  background: linear-gradient(to right, #987654, #D2B48C);
  border-radius: 10px;
  border: 2px solid black;
  padding: 2%;
  font-family: 'Uncial Antiqua', system-ui;
}
.menu_headers{
  margin-top: 20%;
  font-family: 'Comic Sans MS';
}
.menu_header{
  font-size: 120%;
  color: black;
  text-decoration: none;
}
.menu_header:visited{
  color: black;
  text-decoration: none;
}
.menu_header:hover{
  text-decoration: underline;
  color: red;
  cursor: pointer;
}
.arena_header{
  color: black;
}
</style>
