<template>
    <b-row>
        <div class="create-issue p-3 border">
            <div class="d-flex justify-content-center" v-if="!tokenRetrieved">
                <b-spinner variant="primary" label="Loading..."></b-spinner>
            </div>
            <h1 v-if="tokenRetrieved">{{ msg }}</h1>
            <b-form v-on:submit.prevent="createIssue" v-if="tokenRetrieved">
                <b-form-group label="Name:" label-for="name">
                    <b-form-input id="name" v-model.trim="issue.title" placeholder="Enter name..." type="text" />
                </b-form-group>
                <b-form-group label="Description:" label-for="description">
                    <b-form-textarea id="description" v-model.trim="issue.body" placeholder="Enter description..."></b-form-textarea>
                </b-form-group>

                <b-button variant="primary" type="submit">
                    <span v-if="!buttonLoading">Create Issue</span>
                    <span v-if="buttonLoading">
                        <b-spinner small></b-spinner>
                        <span class="sr-only">Loading...</span>
                    </span>
                </b-button>
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
                tokenRetrieved: false,
                buttonLoading: false
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

                this.buttonLoading = true;

                fetch(config.api.url, {
                    headers: headers,
                    method: 'POST',
                    body: JSON.stringify(this.issue)
                })
                .then(response => {
                    this.buttonLoading = false;
                    this.$bvToast.toast(`Issue created successfully!`, {
                        title: 'Create Issue',
                        toaster: "b-toaster-bottom-right",
                        autoHideDelay: 2000,
                        appendToast: false,
                        solid: true,
                        variant: 'success'
                    })
                })
                .catch(error => console.log(error));
            }
        }
    };

</script>
