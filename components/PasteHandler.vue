<template>
  <div></div>
</template>

<script>
export default {
  created() {
    // const component = this;
    document.onpaste = this.pasteImage;
    // window.addEventListener("paste", this.pasteImage);
  },
  props: ["onPaste"],
  methods: {
    // https://stackoverflow.com/questions/6333814/how-does-the-paste-image-from-clipboard-functionality-work-in-gmail-and-google-c
    pasteImage: function (event) {
      var items = (event.clipboardData || event.originalEvent.clipboardData)
        .items;

      // find pasted image among pasted items
      var blob = null;
      for (var i = 0; i < items.length; i++) {
        if (items[i].type.indexOf("image") === 0) {
          blob = items[i].getAsFile();
        }
      }
      // load image if there is a pasted image
      if (blob !== null) {
        var reader = new FileReader();
        reader.onload = (event) => {
          // console.log(event.target.result); // data url!
          this.onPaste(event.target.result);
        };
        reader.readAsDataURL(blob);
      }
    },
  },
};
</script>