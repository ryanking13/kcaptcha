<template>
  <section class="container">
    <div>
      <no-ssr>
        <paste-handler :onPaste="updateCaptchaImage" />
      </no-ssr>
      <dropzone :onAdd="check" />
      <img
        v-if="captchaSrc"
        id="captcha"
        :src="captchaSrc"
        alt="Uploaded CAPTCHA Image"
        style="width: 160px; height: 60px"
      />
    </div>
  </section>
</template>

<style scoped>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>

<script>
import Card from "~/components/Card";
import Dropzone from "~/components/Dropzone";
import PasteHandler from "~/components/PasteHandler";

export default {
  name: "HomePage",

  data() {
    return {
      captchaSrc: "",
    };
  },

  components: {
    Card,
    Dropzone,
    PasteHandler,
  },

  methods: {
    updateCaptchaImage: function (src) {
      this.captchaSrc = src;
    },
    check: function (file) {
      var reader = new FileReader();
      reader.onload = (event) => {
        // console.log(event.target.result); // data url!
        this.updateCaptchaImage(event.target.result);
      };
      reader.readAsDataURL(file);
    },
  },
};
</script>
