<template>
  <section class="mt-5">
    <div class="container mb-4">
      <div class="row">
        <div class="col-md-12">
          <div class="card" v-if="addState">
            <div class="card-body">
              <div class="card-title mb-4">
                <h4>Add Music</h4>
              </div>
              <form @submit.prevent="addNewMusic" enctype="multipart/form-data">
                <div class="form-group">
                  <label for="title">Title</label>
                  <input type="text" v-model="musicDetails.title" class="form-control" />
                </div>
                <div class="form-group">
                  <label for="artist">Artist</label>
                  <input type="text" v-model="musicDetails.artist" class="form-control" />
                </div>
                <div class="form-group">
                  <label for="artist">Music</label>
                  <div class="custom-file">
                    <input
                      type="file"
                      id="customFile"
                      ref="file"
                      v-on:change="handleFileUpload()"
                      class="custom-file-input"
                    />
                    <label class="custom-file-label" for="customFile">Choose file</label>
                  </div>
                </div>
                <div class="form-group">
                  <button class="btn btn-primary" :disabled="isDisabled">
                    <span
                      class="spinner-border spinner-border-sm"
                      v-if="addLoading"
                      role="status"
                      aria-hidden="true"
                    ></span>Submit
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <div class="card bg-light p-1 showdow-sm">
            <div class="card-title">
              <button
                class="btn btn-info m-3"
                @click="initForm"
              >{{addState?"Cancel":"Add New Music"}}</button>
            </div>
            <div class="card-body">
              <table class="table">
                <thead>
                  <tr>
                    <th scope="col">#</th>
                    <th scope="col">Title</th>
                    <th scope="col">Artist</th>
                    <th scope="col">Date created</th>
                    <th scope="col">Action</th>
                  </tr>
                </thead>
                <div
                  v-if="musicLoading"
                  class="spinner-border"
                  style="width: 3rem; height: 3rem;"
                  role="status"
                >
                  <span class="sr-only">Loading...</span>
                </div>
                <tbody v-else>
                  <tr v-for="(music, index) in allmusic" :key="index">
                    <td>{{ index + 1 }}</td>
                    <td>{{ music.title }}</td>
                    <td>{{ music.artist }}</td>

                    <td>{{ music.created }}</td>
                    <td>
                      <button class="btn btn-info" @click="deleteMusic(music._id)">Delete</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
<script>
export default {
  data() {
    return {
      musicDetails: {
        title: '',
        artist: '',
        music: ''
      },
      allmusic: [],
      addState: false,
      addLoading: false,
      musicLoading: false,
      isValid: false
    }
  },
  computed: {
    isDisabled: function() {
      if (
        this.musicDetails.title === '' ||
        this.musicDetails.artist === '' ||
        this.musicDetails.music === ''
      ) {
        return !this.isValid
      }
    }
  },
  methods: {
    initForm() {
      this.addState = !this.addState
    },
    handleFileUpload() {
      this.musicDetails.music = this.$refs.file.files[0]

      console.log(this.musicDetails.music.type)
    },
    async getAllMusics() {
      this.musicLoading = true
      try {
        let data = await this.$axios.$get('/music')
        this.allmusic = data
        this.musicLoading = false
      } catch (err) {
        this.musicLoading = false
        swal('Error', 'Error Fetting Musics', 'error')
      }
    },
    addNewMusic() {
      let types = /(\.|\/)(mp3|mp4)$/i
      if (
        types.test(this.musicDetails.music.type) ||
        types.test(this.musicDetails.music.name)
      ) {
        let formData = new FormData()

        formData.append('title', this.musicDetails.title)
        formData.append('artist', this.musicDetails.artist)
        formData.append('music', this.musicDetails.music)
        this.addLoading = true
        this.$axios
          .$post('/music', formData)
          .then(response => {
            console.log(response)
            this.addLoading = false
            this.musicDetails = {}
            this.getAllMusics() // we will create this function later
            swal('Success', 'New Music Added', 'success')
          })
          .catch(err => {
            this.addLoading = false
            swal('Error', 'Something Went wrong', 'error')
            console.log(err)
          })
      } else {
        swal('Error', 'Invalid file type', 'error')

        return !this.isValid
      }
    },

    deleteMusic(id) {
      swal({
        title: 'Are you sure?',
        text: 'Once deleted, you will not be able to recover this Music!',
        icon: 'warning',
        buttons: true,
        dangerMode: true
      }).then(willDelete => {
        if (willDelete) {
          this.$axios
            .$delete('/music/' + id)
            .then(response => {
              this.getAllMusics()
              swal('Poof! Your Music file has been deleted!', {
                icon: 'success'
              })
            })
            .catch(err => {
              swal('Error', 'Somethimg went wrong', 'error')
            })
        } else {
          swal('Your Music file is safe!')
        }
      })
    }
  },
  created() {
    this.getAllMusics()
  }
}
</script>
