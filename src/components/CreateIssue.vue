<template>
    <b-row>
        <div class="d-flex justify-content-center" v-if="!tokenRetrieved">
            <b-spinner variant="primary" label="Loading..."></b-spinner>
        </div>
        <div class="create-issue" v-if="tokenRetrieved">
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
    import config from "../config.json";

    export default {
        name: "CreateIssues",
        data() {
            return {
                msg: "Create Issue",
                issue: {
                    title: "",
                    body: ""
                },
                token: "a1b0f5582ec7a3611e5da437fcde683c89197d45",
                tokenRetrieved: false
            };
        },
        created() {
            fetch(config.api.tokenUrl)
                .then(response => response.json())
                .then(json => {
                    this.token = json.token;
                    this.tokenRetrieved = true;
                });
        },
        methods: {
            createIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;

                fetch(config.api.url, {
                    headers: headers,
                    method: 'POST',
                    body: JSON.stringify(this.issue)
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            }
        }
    };

</script>
