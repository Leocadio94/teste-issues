<template>
  <div id="app">
    <ListIssues/>
    <CreateIssue/>
  </div>
</template>

<script>
import ListIssues from './components/ListIssues'
import CreateIssue from './components/CreateIssue'

export default {
  name: 'App',
  components: {
    ListIssues,
    CreateIssue
  }
}
</script>
