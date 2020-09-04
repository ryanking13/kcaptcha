<template>
  <section class="container">
    <div>
      <no-ssr>
        <paste-handler :onPaste="updateCaptchaImage" />
      </no-ssr>
      <dropzone :onAdd="loadCaptchaImage" />
      <img
        v-show="captchaSrc"
        id="captcha"
        :src="captchaSrc"
        alt="Uploaded CAPTCHA Image"
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
import * as tf from "@tensorflow/tfjs";

export default {
  name: "HomePage",
  async created() {
    this.model = await tf.loadGraphModel("http://localhost:3000", {
      fromTFHub: true,
    });
  },

  data() {
    return {
      captchaSrc: "",
      model: null,
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
      setTimeout(() => {
        this.predictCaptcha();
      }, 10); // prevent pre predicting
    },
    loadCaptchaImage: function (file) {
      var reader = new FileReader();
      reader.onload = (event) => {
        // console.log(event.target.result); // data url!
        this.updateCaptchaImage(event.target.result);
      };
      reader.readAsDataURL(file);
    },
    predictCaptcha: function () {

      // Get HTMLImageElement from the document
      const imgElem = document.getElementById("captcha");
      // Convert HTMLImageElement to pixel data
      const img = tf.browser.fromPixels(imgElem).toFloat().resizeNearestNeighbor([224, 224])
      // img.print()

      // Normalize image to fit Model input format
      const imgNormalized = img.div(tf.scalar(255));
      const imgNormalizedBatch = imgNormalized.expandDims(0);

      // console.log(imgNormalizedBatch)
      const result = this.model.predict(imgNormalizedBatch);

      tf.argMax(result, 1).print()
    },
  },
};
</script>
