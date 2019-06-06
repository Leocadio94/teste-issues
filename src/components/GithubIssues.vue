<template>
  <div class="github-issues">
    <ul>
        <li v-for="issue in issues">
            ({{ issue.state }})  - {{ issue.title }}
            <p>{{issue.body}}</p>
            <img :src=issue.user.avatar_url />
        </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'GithubIssues',
  data () {
    return {
        issues: [],
        msg: 'Github Issues'
    }
  },
  created() {
      fetch('https://api.github.com/repos/Leocadio94/teste-issues/issues')
        .then(response => response.json())
        .then(json => {
            this.issues = json
        })
  }
}
</script>
