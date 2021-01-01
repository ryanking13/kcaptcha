<template>
  <section class="container">
    <div>
      <github-ribbon />
      <h1 class="title">KCAPTCHA solver</h1>
      <h5 class="subtitle">그누보드 KCAPTCHA 분류기</h5>
      <client-only>
        <paste-handler :onPaste="updateCaptchaImage" />
      </client-only>
      <dropzone :onAdd="readCaptchaImage" />
      <img
        v-show="captchaSrc"
        id="captcha"
        :src="captchaSrc"
        alt="Uploaded CAPTCHA Image"
      />
      <!-- <canvas id='canvas2' width="128" height="128" style=""></canvas> -->
    </div>
    <sui-dimmer :active="isLoading === true" inverted>
      <sui-loader content="모델 로딩 중..." />
    </sui-dimmer>
  </section>
</template>

<script>
import Dropzone from "~/components/Dropzone";
import PasteHandler from "~/components/PasteHandler";
import GithubRibbon from "~/components/GithubRibbon";
import * as tf from "@tensorflow/tfjs";

export default {
  name: "Home",
  async mounted() {
    // this.model = await tf.loadGraphModel(window.location + "model/model.json", {
    //   fromTFHub: false,
    // });
    console.log("[*] Model loading...");
    this.loading(true);
    this.model = await tf.loadGraphModel(
      location.href + "/models/l2_160_60/model.json"
    );
    console.log("[*] Model loading... Done");
    console.log("[*] Model warming up...");
    this.model.predict(tf.zeros([1, 60, 160, 3]));
    this.loading(false);
    console.log("[*] Model warming up... Done");
  },

  data() {
    return {
      captchaSrc: "",
      model: null,
      isLoading: false,
    };
  },

  components: {
    Dropzone,
    PasteHandler,
    GithubRibbon,
  },

  methods: {
    loading: function (on) {
      if (on) {
        this.isLoading = true;
      } else {
        this.isLoading = false;
      }
    },
    updateCaptchaImage: function (src) {
      this.captchaSrc = src;
    },

    // read CAPTCHA image from Clipboard
    readCaptchaImage: function (file) {
      var reader = new FileReader();
      reader.onload = (event) => {
        // console.log(event.target.result); // data url!
        this.updateCaptchaImage(event.target.result);
      };
      reader.readAsDataURL(file);
    },

    // Convert HTMLImageElement to pixel data and proprocess it
    tfPreprocessImage: function (img) {
      const mean = tf.tensor1d([0.485, 0.456, 0.406]);
      const std = tf.tensor1d([0.229, 0.224, 0.225]);
      const size = [60, 160, 3]; // [height, width, channel]
      return img.reshape(size).toFloat().div(tf.scalar(255)).sub(mean).div(std);
    },

    tfGetImage: function (imageId) {
      // Get HTMLImageElement from the document
      const imgElem = document.getElementById(imageId);
      const img = tf.browser.fromPixels(imgElem);
      return this.tfPreprocessImage(img);
    },

    tfPredictCaptcha: function () {
      const img = this.tfGetImage("captcha");
      const imgBatch = img.expandDims(0);

      // console.log(imgNormalizedBatch)
      const result = this.model.predict(imgBatch);
      return this.tfDecodePredction(result, 10, 2);
    },
    tfDecodePredction: function (tensor, numCharSet) {
      // tensor.print();

      // slice tensor to each captcha character
      var sliced = tensor.dataSync().reduce((resultArray, item, index) => {
        const chunkIndex = Math.floor(index / numCharSet);

        if (!resultArray[chunkIndex]) {
          resultArray[chunkIndex] = []; // start a new chunk
        }

        resultArray[chunkIndex].push(item);

        return resultArray;
      }, []);

      // extract predictions for each character
      const argMax = (array) =>
        array.map((x, i) => [x, i]).reduce((r, a) => (a[0] > r[0] ? a : r))[1];
      const predicted = sliced.map(argMax).join("");

      console.log(predicted);
    },
  },
  watch: {
    captchaSrc() {
      // wait until changed captchaSrc is applied to img
      setTimeout(() => {
        this.tfPredictCaptcha();
      }, 50);
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css?family=Open+Sans|Quicksand|Roboto");
.container {
  max-width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 350;
  font-size: 56px;
  color: #b71c1c;
  letter-spacing: 1px;
}
.subtitle {
  font-weight: 300;
  font-size: 24px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}
</style>
