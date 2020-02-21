<template>
  <section v-if="allMusic">
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <h3 class="text-center">Player</h3>
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <span>{{this.current.title}} - {{this.current.artist}}</span>
        </div>
      </div>
    </div>
  </section>
</template>
<script>
export default {
  data() {
    return {
      current: {
        title: '',
        artist: ''
      },
      song: true,
      isplaying: false,
      allMusic: null,
      index: 0,
      player: ''
    }
  },
  methods: {
     async initPlayer() {
      if (this.allMusic !== []) {
        this.current = await this.allMusic[this.index]
        this.player.src = `http://localhost:4000/${this.current.music.path}`
      } else {
        this.song = true
      }
    },
      async getAllSongs() {
        try {
        let response = await this.$axios.$get('/music')
        console.log(response)
        if (response === []) {
          this.song = true
          this.current = null
        } else {
          this.song = false
          this.allMusic = response
        }
        await this.initPlayer()
      } catch (err) {
        this.current = null
        console.log(err)
      }
    }
  },
  created() {
 if (process.client) {
      this.player = new Audio()
    }
    this.getAllSongs()
  }
}
</script>
<style  scoped>
</style>

