<template>
  <div>
    <input type="text" v-model="num">
    <input type="text" v-model="name">
    <input type="text" v-model="title">
    <v-btn @click="input(num, name, title)">확인</v-btn>
  </div>
</template>

<script>
export default {
  name: "Home",
  components: {},
  data() {
    return {
      list: [],
      num: null,
      name: "",
      title: ""
    };
  },
  methods: {
    input() {
      this.list.push("num")
    }
  }
};
</script>