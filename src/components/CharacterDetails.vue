<template>
  <div>
    <button @click="closeDetails">Close</button>
    <h1>{{ personObject.name }}</h1>
    <p>Gender: {{ personObject.gender }}</p>
    <p>Height: {{ personObject.height }} centimeters</p>
    <p>Mass: {{ personObject.mass }} kilograms</p>
  </div>
</template>

<script>
import { getHomeworld } from "@/api/api.js";
export default {
  data() {
    return {
      homeWorld: {},
    };
  },
  props: {
    personObject: {
      type: Object,
    },
  },
  created: async function () {
    const homeWorld = await getHomeworld(this.personObject.homeworld);
    this.homeWorld = homeWorld;
  },
  methods: {
    closeDetails() {
      this.$emit("clicked");
    },
  },
};
</script>

<style lang="scss" scoped>
</style>