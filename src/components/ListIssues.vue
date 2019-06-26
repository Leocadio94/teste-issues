<template>
    <b-row>
        <div class="list-issues">
            <h1>{{ msg }}</h1>
            <div class="d-flex justify-content-center" v-if="!loaded">
                <b-spinner variant="primary" label="Loading..."></b-spinner>
            </div>
             <div role="tablist" v-if="loaded && tokenRetrieved">
                 <b-card no-body class="mb-1" v-for="issue in issues" :key="issue" @click="setCurrentIssue(issue)" >
                    <b-card-header header-tag="header" class="p-1" role="tab">
                        <b-button block href="#" v-b-toggle="'accordion'+issue.number" variant="info" :class=" { 'text-left': true, active: isCurrentIssue(issue) } ">
                            {{ issue.title }}
                        </b-button>
                    </b-card-header>
                    <b-collapse :id="'accordion'+issue.number" accordion="accordion-issues" role="tabpanel">
                        <b-card-body>
                            <b-card-text>
                                <strong>User:</strong>
                                <img :src="issue.user.avatar_url" width="20px" height="20px"> 
                                {{issue.user.login}} 
                            </b-card-text>

                            <b-card-text><strong>Description:</strong> {{ issue.body }}</b-card-text>

                            <b-card-text><strong>Status:</strong> {{ issue.state }}</b-card-text>

                            <b-card-text v-if="issue.labels.length > 0">
                                <strong>Labels:</strong> 
                                <span v-for="label in issue.labels" :key="label" :style="{ 'background-color': '#'+label.color }">
                                    {{ label.name}}
                                </span>
                            </b-card-text>

                            <b-card-text>
                                <input v-model.trim="currentIssue.title" placeholder="Name" type="text">
                                <textarea v-model.trim="currentIssue.body" placeholder="Description"></textarea>

                                <b-button @click="editIssue">Edit Issue</b-button>
                                <b-button @click="lockIssue" v-if="!issue.locked">Lock Issue</b-button>
                                <b-button @click="unlockIssue" v-if="issue.locked">Unlock Issue</b-button>
                            </b-card-text>
                        </b-card-body>
                    </b-collapse>
                </b-card>
            </div>
        </div>
    </b-row>
</template>

<script>
    import config from "../config.json";

    export default {
        name: "ListIssues",
        data() {
            return {
                loaded: false,
                issues: [],
                currentId: 0,
                currentIssue: {},
                token: "a1b0f5582ec7a3611e5da437fcde683c89197d45",
                tokenRetrieved: false,
                msg: "List Issues"
            };
        },
        created() {
            fetch(config.api.url)
                .then(response => response.json())
                .then(json => {
                    this.issues = json;
                    this.loaded = true;
                });
        
            fetch(config.api.tokenUrl)
                .then(response => response.json())
                .then(json => {
                    this.token = json.token;
                    this.tokenRetrieved = true;
                });
        },
        methods: {
            setCurrentIssue(thisIssue) {
                this.currentId = thisIssue.number;
                this.currentIssue = {
                    title: thisIssue.title,
                    body: thisIssue.body,
                };
            },
            isCurrentIssue(issue) {
                return this.currentId == issue.number;
            },
            editIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;

                fetch(config.api.url + '/' + this.currentId, {
                    headers: headers,
                    method: 'PATCH',
                    body: JSON.stringify(this.currentIssue)
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
            lockIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;

                fetch(config.api.url + '/' + this.currentId + '/lock', {
                    headers: headers,
                    method: 'PUT',
                    body: JSON.stringify({ "locked": true, "active_lock_reason": "too heated" })
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
            unlockIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;
                
                fetch(config.api.url + '/' + this.currentId + '/lock', {
                    headers: headers,
                    method: 'DELETE'
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
        }
    };

</script>
