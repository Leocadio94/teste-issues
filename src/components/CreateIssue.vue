<template>
    <b-row>
        <div class="create-issue">
            <h1>{{ msg }}</h1>
            <b-form v-on:submit.prevent="createIssue">
                <b-form-group label="Name:" label-for="name">
                    <b-form-input id="name" v-model.trim="issue.title" placeholder="Enter name..." type="text" />
                </b-form-group>
                <b-form-group label="Description:" label-for="description">
                    <b-form-textarea id="description" v-model.trim="issue.body" placeholder="Enter description..."></b-form-textarea>
                </b-form-group>

                <b-button variant="primary" type="submit">Create Issue</b-button>
            </b-form>
        </div>
    </b-row>
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
                        "Authorization": "token " + Vue.prototype.$token
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
