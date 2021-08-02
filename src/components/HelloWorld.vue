<template>
  <div class="wrapper">
    <br />
    <v-row>
      <v-container>
        <v-row no-gutters style="height: 200px">
          <v-col>
            <label for="file-upload" class="custom-file-upload">
              <i class="fa fa-cloud-upload"></i> Custom Upload
            </label>
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

            <!-- <div>
              <div v-if="state === true">

                <img
                  v-if="img_res"
                  :src="img_res"
                  class="img"
                  id="img"
                  width="200px"
                />
                <div class="aaa" id="aaa">{{data.confidence}}</div>
                  <v-card-title>
                  {{ data.name }}
                </v-card-title>

              </div> -->

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
            <!-- <v-card v-if="state === true">
  <v-img
                  class="img"
                  id="img"
                  src="img_res"
                  style="background-color: red,box-sizing: border-box;"
                  width="200px"
                ></v-img>
                <v-card-title>
                  {{ data.name }}
                </v-card-title>

                <v-card-subtitle>
                  {{ data.parent }}
                </v-card-subtitle>
              </v-card> -->
            <!-- <img id="img" class="img" style="background-color:transparent"  v-if="image" :src="image" width="200px" /> -->
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
      // axios upload base64

      // const form = new FormData()
      // form.append(this.file, this.file.name)

      // this.axios.post('/upload', { image: this.image })
      // console.log({ image: this.image })
      this.img_res = this.image
      this.imgBase64 = this.image.split(',')[1]

      // console.log(this.substr)

      // this.axios.post('/upload', form)
      this.axios
        .post('http://localhost:8000/img', {
          image: this.imgBase64
        })
        .then((response) => {
          // var bottom: 0
          // var left: 0
          // var right: 0
          // var top: 0
          // console.log(response.data)
          for (const [key, value] of Object.entries(response.data)) {
            var e = key
            console.log(e, value)
            this.data = value[0]
            console.log(this.data, 'data')
            // console.log(value[0].bounding_box)
            this.css = value[0].bounding_box
          }
          // document.getElementById('img').setAttribute('src', this.img_res)
          console.log(this.data.confidence)

          //           top: 53.8889px;
          // left: 161.312px;
          // height: 243.056px;
          // width: 229.453px;
          // position: absolute;
          // outline-color: rgb(243, 164, 51);
          // z-index: 5;
          // opacity: 1;
          // outline-width: 2px;
          this.state = true

          document.getElementById('img').style.position = 'absolute'
          document.getElementById('img').style.border = '5px solid #ffffff'
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
input[type="file"] {
  display: none;
}
.custom-file-upload {
  border: 1px solid #ccc;
  display: inline-block;
  padding: 6px 12px;
  cursor: pointer;
}
.wrapper {
  position: relative;
}
</style>
