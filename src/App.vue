<template>
  <HelloWorld msg="Test task" />
  <!-- <Header /> -->
  <MainView />
  <notifications position="bottom right" classes="my-custom-class" />
  <!-- <Footer /> -->
</template>

<script>
// import Header from "./components/Header.vue";
import HelloWorld from "./components/HelloWorld.vue";
// import Footer from "./components/Footer.vue";
import MainView from "./components/Main_View.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
    // Header,
    // Footer,
    MainView,
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
