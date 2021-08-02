<template>
  <div class="wrapper">
    <br />
    <v-row>
      <v-container>
        <v-row no-gutters style="height: 200px">
          <v-col>
            <input type="file" accept="image/*" @change="fileSelected" />
            <br />
            <div v-if="state === false">
              <img v-if="image" :src="image" width="200px" />
            </div>
            <br />
            <v-btn color="primary" @click="upload"> Uplaod</v-btn>
            <br />
            <br />
            <br />
            <v-card v-if="state === true" class="mx-auto" max-width="344">
              <div v-if="state === true">
                <img
                  v-if="img_res"
                  id="img"
                  :src="img_res"
                  class="img"
                  width="200px"
                />
                <v-card-title>
                  {{ data.name }}
                  <br />
                  {{ data.parent }}
                </v-card-title>
                <v-card-subtitle>
                  {{ "confidence:" + "  " + data.confidence }}
                </v-card-subtitle>
              </div>
            </v-card>
            <br />
          </v-col>
        </v-row>
      </v-container>
    </v-row>
  </div>
</template>

<script>
export default {
  data () {
    return {
      image: '',
      state: false,
      img_res: '',
      imgBase64: '',
      data: {
        confidence: 0,
        name: '',
        parent: ''
      },
      css: {
        bottom: '',
        left: '',
        right: '',
        top: ''
      }
    }
  },
  methods: {
    fileSelected (event) {
      this.state = false

      const file = event.target.files.item(0)
      const reader = new FileReader()
      reader.addEventListener('load', this.imageLoaded)
      reader.readAsDataURL(file)
    },
    imageLoaded (event) {
      this.image = event.target.result
    },
    upload () {
      this.img_res = this.image
      this.imgBase64 = this.image.split(',')[1]
      this.axios
        .post('http://192.168.43.182:8000/img', {
          image: this.imgBase64
        })
        .then((response) => {
          for (const [key, value] of Object.entries(response.data)) {
            var e = key
            console.log(e, value)
            this.data = value[0]
            console.log(this.data, 'data')
            // console.log(value[0].bounding_box)
            this.css = value[0].bounding_box
          }
          // document.getElementById('img').setAttribute('src', this.img_res)
          this.state = true

          // document.getElementById('img').style.position = 'absolute'
          // document.getElementById('img').style.border = '5px solid #ffffff'
          // document.getElementById('img').style.bottom = this.css.bottom + 'px'
          // document.getElementById('img').style.left = this.css.left + 'px'
          // document.getElementById('img').style.right = this.css.right + 'px'
          // document.getElementById('img').style.top = this.css.top + 'px'
        })
    }
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  position: relative;
}
</style>
