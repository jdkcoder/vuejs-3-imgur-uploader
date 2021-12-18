<style>
body {
  font-family: 'Nunito', sans-serif;
  background: #000;
  color: #fff;
  display: grid;
  place-items: center;
  text-align: center;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #fff;
  margin-top: 60px;
}
#img_thumb {
  height: 300px;
}
img {
  border-radius: 0.5rem;
}
</style>

<template>
  <img src="https://i.imgur.com/X4z0q8g.png" />
  <br /><br />
  <input
    v-show="!imgur_url"
    type="file"
    id="image_file"
    @change="onFileChange"
    class="imgur"
    accept="image/*"
    required
  />
  <button v-show="imgur_url" type="button" @click="refreshNewImg">
    Upload New One
  </button>
  <br />
  <p>URL: {{ imgur_url }}</p>
  <br />
  <img :src="img_thumb" id="img_thumb" />
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      loading_gif:
        'https://i.pinimg.com/originals/a4/f2/cb/a4f2cb80ff2ae2772e80bf30e9d78d4c.gif',
      img_thumb_sample: 'https://i.imgur.com/UoRevba.gif',
      img_thumb: null,
      imgur_url: null,
    };
  },
  methods: {
    onFileChange(event) {
      let files = event.target.files || e.dataTransfer.files;
      if (!files.length) {
        return;
      }

      this.img_thumb = this.loading_gif;
      this.imgur_url = 'Uploading...';

      let formdata = new FormData();

      formdata.append('image', files[0]);

      axios
        .post(`https://api.imgur.com/3/image`, formdata, {
          headers: {
            'Content-type': 'application/x-www-form-urlencoded',
            Authorization: 'Client-ID Client_ID_HERE',
          },
          onUploadProgress: (percentage) => {
            let uploadPercent = Math.round(
              (percentage.loaded / percentage.total) * 100
            );
            console.log(`Uploaded ${uploadPercent} %`);
          },
        })
        .then((res) => {
          console.log(res.data);
          this.imgur_url = res.data.data.link;
          this.img_thumb = res.data.data.link;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    refreshNewImg() {
      this.imgur_url = '';
      document.getElementById('image_file').value = '';
      this.img_thumb = this.img_thumb_sample;
    },
  },
  created() {
    this.img_thumb = this.img_thumb_sample;
  },
};
</script>
