<template>
  <div id="app" style="overflow: hidden">
    <div
      style="
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100vh;
        width: 100vw;
      "
    >
      <div>
        <VueFetti v-if="visible" :options="vfconfig"></VueFetti>
        <button @click="explode">Go time</button>
      </div>
    </div>
  </div>
</template>
<script>
import Vue from "vue";
import VueFetti from "./components/VueFetti";

export default {
  name: "VueFettiApp",

  components: {
    VueFetti,
  },

  data() {
    return {
      vfconfig: {
        angle: 90,
        spread: 360,
        startVelocity: 40,
        elementCount: 70,
        dragFriction: 0.12,
        duration: 3000,
        stagger: 3,
        width: "10px",
        height: "10px",
        perspective: "500px",
        colors: ["#a864fd", "#29cdff", "#78ff44", "#ff718d", "#fdff6a"],
      },
      visible: Vue.observable(false),
    };
  },

  methods: {
    async explode() {
      this.visible = false;
      await Vue.nextTick();
      this.visible = true;
    },
  },
};
</script>
