<template>
    <div class="create-issue">
        <h1>Create Issue</h1>
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
                        "Authorization": "token 45ee2f38cb8a3ec3c86523e2642667c1e4d5f5d7"
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
