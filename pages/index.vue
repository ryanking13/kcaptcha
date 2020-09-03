<template>
  <section class="container">
    <no-ssr>
      <paste-handler />
    </no-ssr>
    <dropzone />
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

  components: {
    Card,
    Dropzone,
    PasteHandler,
  },
};
</script>
