<script>
import { RouterView } from 'vue-router'
import { userStore } from '@/stores/user.js';
import { gameStore } from '@/stores/game.js';
import { loadingStore } from '@/stores/loading.js';
import { clueStore } from '@/stores/clue.js';
import axios from 'axios'
export default {
  name: 'App',
  components: {
    RouterView
  },
  data() {
    return {
      user: userStore(),
      game: gameStore(),
      loading: loadingStore(),
      clues: clueStore()
    };
  },
  async mounted() {
    await this.$connectSocket(this.$socket);

    if (this.$gameStore.empty && this.$userStore.isLoggedIn) {
      console.log('Rejoining game');
      this.$socket.emit('join_game', {game_id: this.$gameStore.game.id, game_code: this.$gameStore.code});
    }

    if (this.user.token) {
      axios.defaults.headers.common['Authorization'] = this.user.token;
    }

    this.$socket.on('disconnect', () => {
      console.log('Socket disconnected');
    });

    this.$socket.on('error', (data) => {
      console.error('Socket error:', data.message);
    });

    this.$socket.on('game_updated', (data) => {
      this.$gameStore.game = data;
    });

    this.$socket.on('clue_updated', (data) => {
      this.$clueStore.update(data);
    });

    this.$socket.on('player_list', (newPlayers) => {
      if (this.$gameStore?.players){
        let existingPlayerIds = this.$gameStore.players.map(player => player.player_id);
      newPlayers.forEach(player => {
        if(player.host || player.player_id === this.$userStore.id){
          return 
        }
        if (!existingPlayerIds.includes(player.player_id)) {
          this.$info(`${player.display_name} joined the game!`);
        }
      });
      }
      this.$gameStore.players = newPlayers;
    });
  },
}
</script>


<template>
  <RouterView />
</template>

