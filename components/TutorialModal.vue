<template>
  <sui-modal class="center" basic v-model="open">
    <h1 is="sui-header" class="message">사용법</h1>
    <h3 class="message">KCAPTCHA 이미지를 업로드 또는 붙여넣기합니다</h3>
    <h4 is="sui-header" color="orange" class="center">
      길이 2인 숫자 CAPTCHA만 사용가능합니다
    </h4>
    <h3 is="sui-header" class="center">예시 이미지</h3>
    <div
      v-for="example in exampleImages"
      :key="example.path"
      class="modal-component"
    >
      <div>
        <img :src="example.path" />
      </div>
      <div>
        <sui-button basic color="olive" @click="() => copy(example)">
          {{ example.copyText }}
        </sui-button>
      </div>
      <div>
        <sui-button basic color="green" @click="() => download(example)">
          {{ example.downloadText }}
        </sui-button>
      </div>
    </div>
    <br />
    <div class="modal-component">
      <sui-modal-actions>
        <sui-button basic size="large" color="orange" @click.native="() => changeModalState(false)"> 닫기 </sui-button>
      </sui-modal-actions>
    </div>
  </sui-modal>
</template>

<script>
export default {
  props: ["changeModalState"],
  data() {
    return {
      open: true,
      exampleImages: [
        {
          path: "examples/01.png",
          copyText: "복사",
          downloadText: "다운로드",
        },
        {
          path: "examples/08.png",
          copyText: "복사",
          downloadText: "다운로드",
        },
        {
          path: "examples/63.png",
          copyText: "복사",
          downloadText: "다운로드",
        },
        {
          path: "examples/88.png",
          copyText: "복사",
          downloadText: "다운로드",
        },
        {
          path: "examples/97.png",
          copyText: "복사",
          downloadText: "다운로드",
        },
      ],
    };
  },
  watch: {
    open: function (val) {
      this.changeModalState(val);
    },
  },
  methods: {
    copy: async function (example) {
      const originalText = example.copyText;
      example.copyText = "복사 중...";

      const response = await fetch(example.path);
      const blob = await response.blob();
      await navigator.clipboard.write([
        new ClipboardItem({ "image/png": blob }),
      ]);
      example.copyText = "복사됨!";
      setTimeout(() => {
        example.copyText = originalText;
      }, 4000);
    },
    download: async function (example) {
      await fetch(example.path, { mode: "no-cors" })
        .then((response) => response.blob())
        .then((blob) => {
          const url = window.URL.createObjectURL(blob);
          const a = document.createElement("a");
          a.style.display = "none";
          a.href = url;
          a.download = example.path.split("/").pop();
          document.body.appendChild(a);
          a.click();
          window.URL.revokeObjectURL(url);
        });
    },
  },
};
</script>

<style type="text/css">
.center {
  text-align: center;
}

.message {
  margin: 20px;
  text-align: center;
}

.modal-component {
  margin: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.modal-component > div {
  /* flex: 1 1 auto; */
  /* border:1px red solid; */
  text-align: center;
  margin-top: 5px;
  margin-bottom: 5px;
  margin-left: 10px;
  margin-right: 10px;
}
</style>
