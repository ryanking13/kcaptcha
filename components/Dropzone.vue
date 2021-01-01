<template>
  <dropzone id="dropzone" ref="el" :options="options" @vdropzone-file-added="onAdd" :useCustomSlot=true>
    <h3 class="dropzone-custom-title">Drag and Drop <br/>or<br/> Copy and Paste <br/><br/> KCAPTCHA Image!</h3>
  </dropzone>
</template>

<style>
body {
  background: rgb(243, 244, 245);
  height: 100%;
  color: rgb(100, 108, 127);
  line-height: 1.4rem;
  font-family: Roboto, "Open Sans", sans-serif;
  font-size: 20px;
  font-weight: 300;
  text-rendering: optimizeLegibility;
}

.dropzone {
  background: white;
  border-radius: 5px;
  border: 2px dashed #b71c1c;
  border-image: none;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
  margin-top: 10px;
  margin-bottom: 10px;
}
</style>

<script>
import Dropzone from "nuxt-dropzone";
import "nuxt-dropzone/dropzone.css";

export default {
  components: {
    Dropzone,
  },
  props: [
    "onAdd",
  ],
  data() {
    return {
      // See https://rowanwins.github.io/vue-dropzone/docs/dist/index.html#/props
      options: {
        url: "http://localhost",
        createImageThumbnails: true,
        autoProcessQueue: false,
        thumbnailWidth: null,
        thumbnailHeight: null,
      },
    };
  },
  mounted() {
    // Everything is mounted and you can access the dropzone instance
    const instance = this.$refs.el.dropzone;
  },
};
</script>