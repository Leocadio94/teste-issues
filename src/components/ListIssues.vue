<template>
    <div class="list-issues">
        <h1>Lista de Issues</h1>
        <ul>
            <li v-for="issue in issues">
                <img :src="issue.user.avatar_url" width="20px" height="20px">
                ({{ issue.state }}) - {{ issue.title }}- {{issue.body}}
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: "ListIssues",
        data() {
            return {
                issues: [],
                msg: "List Issues"
            };
        },
        created() {
            fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues")
                .then(response => response.json())
                .then(json => {
                    this.issues = json;
                });
        }
    };

</script>
