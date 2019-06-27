<style lang="scss">
   @import './src/scss/styles.scss';
</style>


<template>
  <div id="app">
    <Header/>
    <b-container>
      <ListIssues/>
      <hr/>
      <CreateIssue/>
    </b-container>
    <Footer/>
  </div>
</template>

<script>

import Header from "./components/Header";
import ListIssues from "./components/ListIssues";
import CreateIssue from "./components/CreateIssue";
import Footer from "./components/Footer";

export default {
  name: "App",
  components: {
    Header,
    ListIssues,
    CreateIssue,
    Footer
  }
};
</script>
