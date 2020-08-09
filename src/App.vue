<template>
  <div id="app">
    <Tree
      @labelClick="log"
      v-model="data"
      :format="format"
      :customLabel="renderLabel"
    />
  </div>
</template>

<script>
import { Tree } from "./components";

export default {
  name: "App",
  components: {
    Tree,
  },
  data() {
    return {
      data: {
        name: "一级",
        list: [
          {
            name: `二级-1`,
          },
          {
            name: `二级-2`,
            list: [{ name: `三级-1` }, { name: `三级-2` }],
          },
          {
            name: `二级-3`,
            list: [
              { name: `三级-3` },
              { name: `三级-4` },
              { name: `三级-5` },
              { name: `三级-6` },
            ],
          },
        ],
      },
      format: {
        label: "name",
        children: "list",
      },
    };
  },
  watch: {
    data: {
      deep: true,
      handler: data => console.log(data),
    },
  },
  methods: {
    renderLabel(data) {
      return <div>{data.name}</div>;
    },
    log() {
      // console.log(data);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
