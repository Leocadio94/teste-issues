<template>
    <div class="create-issue">
        <h1>{{ msg }}</h1>
        <form v-on:submit.prevent="createIssue">
            <input v-model.trim="issue.title" placeholder="Name" type="text">
            <textarea v-model.trim="issue.body" placeholder="Description"></textarea>

            <button type="submit">Create Issue</button>
        </form>
    </div>
</template>

<script>
    export default {
        name: "CreateIssues",
        data() {
            return {
                msg: "Create Issue",
                issue: {
                    title: "",
                    body: ""
                }
            };
        },
        methods: {
            createIssue() {
                fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues", {
                    headers: { 
                        "Content-Type": "application/json; charset=utf-8",
                        "Authorization": "token a1b0f5582ec7a3611e5da437fcde683c89197d45"
                    },
                    method: 'POST',
                    body: JSON.stringify(this.issue)
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            }
        }
    };

</script>
