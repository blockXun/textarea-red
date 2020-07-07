<template>
  <div class="home">
    <textarea-comp
      :warning="notesBadWords"
      v-model="data"
      style="color: red"
    ></textarea-comp>
      :max="300"
  </div>
</template>

<script>
// @ is an alias to /src
import textareaComp from "@/components/textarea";
import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "Home",
  data() {
    return {
      data: '123aaa',
      notesBadWords: ['aaa','bb'],
    }
  },
  components: {
    textareaComp
  }
};
</script>
