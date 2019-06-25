<template>
    <b-row>
        <div class="list-issues">
            <h1>{{ msg }}</h1>
            <div class="d-flex justify-content-center" v-if="!loaded">
                <b-spinner variant="primary" label="Loading..."></b-spinner>
            </div>
             <div role="tablist" v-if="loaded">
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
                <img :src="issue.user.avatar_url" width="20px" height="20px">
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

                                <button @click="editIssue">Edit Issue</button>
                                <button @click="lockIssue">Lock Issue</button>
                                <button @click="unlockIssue">Unlock Issue</button>
                            </b-card-text>
                        </b-card-body>
                    </b-collapse>
                </b-card>
            </div>
        </div>
    </b-row>
</template>

<script>
    export default {
        name: "ListIssues",
        data() {
            return {
                loaded: false,
                issues: [],
                currentId: 0,
                currentIssue: {},
                token: "a1b0f5582ec7a3611e5da437fcde683c89197d45",
                tokenRetrived: false,
                msg: "List Issues"
            };
        },
        created() {
            fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues")
                .then(response => response.json())
                .then(json => {
                    this.issues = json;
                    this.loaded = true;
                });
            fetch("https://leocadio-netlify-express.netlify.com/.netlify/functions/server/",)
                .then(response => {console.log('amigo sosasassastaqui'); return response.json();})
                .then(json => {
                    this.token = json.token;
                    this.tokenRetrived = true;
                });
        },
        methods: {
            setCurrentIssue(thisIssue) {
                this.currentId = thisIssue.number;
                this.currentIssue = {
                    title: thisIssue.title,
                    body: thisIssue.body,
                };
                console.log('this.token');
                console.log(this.token);
            },
            isCurrentIssue(issue) {
                return this.currentId == issue.number;
            },
            editIssue() {
                fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues/" + this.currentId, {
                    headers: { 
                        "Content-Type": "application/json; charset=utf-8",
                        "Authorization": "token " + this.token
                    },
                    method: 'PATCH',
                    body: JSON.stringify(this.currentIssue)
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
            lockIssue() {
                fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues/" + this.currentId + '/lock', {
                    headers: { 
                        "Content-Type": "application/json; charset=utf-8",
                        "Authorization": "token " + this.token
                    },
                    method: 'PUT',
                    body: JSON.stringify({ "locked": true, "active_lock_reason": "too heated" })
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
            unlockIssue() {
                fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues/" + this.currentId + '/lock', {
                    headers: { 
                        "Content-Type": "application/json; charset=utf-8",
                        "Authorization": "token " + this.token
                    },
                    method: 'DELETE'
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
        }
    };

</script>
