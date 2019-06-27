<template>
    <b-row>
        <div class="list-issues p-3 mt-3 border">
            <h1 v-if="loaded && tokenRetrieved">{{ msg }}</h1>
            <div class="d-flex justify-content-center" v-if="!loaded">
                <b-spinner variant="primary" label="Loading..."></b-spinner>
            </div>
             <div role="tablist" v-if="loaded && tokenRetrieved">
                 <b-card no-body class="mb-1" v-for="issue in issues" :key="issue">
                    <b-card-header header-tag="header" class="p-1" role="tab" @click="setCurrentIssue(issue)" >
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

                            <b-card-text><strong>Created at:</strong> {{ issue.created_at | moment('DD/MM/YYYY') }}</b-card-text>

                            <b-card-text v-if="issue.labels.length > 0">
                                <strong>Labels:</strong> 
                                <span v-for="label in issue.labels" :key="label" :style="{ 'background-color': '#'+label.color }">
                                    {{ label.name}}
                                </span>
                            </b-card-text>

                            <b-card-text v-if="!showEdit">
                                <b-button @click="toggleEdit" variant="info">Edit Issue</b-button>
                            </b-card-text>

                            <b-card-text v-if="showEdit">
                                <b-form-group label="Name:" label-for="name">
                                    <b-form-input id="name" v-model.trim="currentIssue.title" placeholder="Enter name..." type="text" />
                                </b-form-group>
                                <b-form-group label="Description:" label-for="description">
                                    <b-form-textarea id="description" v-model.trim="currentIssue.body" placeholder="Enter description..."></b-form-textarea>
                                </b-form-group>
                            </b-card-text>

                            <b-card-text v-if="showEdit">
                                <b-button @click="editIssue" variant="primary">
                                    <span v-if="!editButtonLoading">Edit Issue</span>
                                    <span v-if="editButtonLoading">
                                        <b-spinner small></b-spinner>
                                        <span class="sr-only">Loading...</span>
                                    </span>
                                </b-button>
                            </b-card-text>

                            <b-card-text>
                                <b-row v-if="!issue.locked">
                                    <b-col md="auto">
                                        <p class="mb-0 mt-1 align-middle h-100">Lock Reason</p>
                                    </b-col>
                                    <b-col>
                                        <b-select v-model="currentLockReason">
                                            <option>off-topic</option>
                                            <option>too heated</option>
                                            <option>spam</option>
                                            <option>resolved</option>
                                        </b-select>
                                    </b-col>
                                    <b-col>
                                        <b-button @click="lockIssue" variant="danger">
                                        <span v-if="!lockButtonLoading">Lock Issue</span>
                                        <span v-if="lockButtonLoading">
                                            <b-spinner small></b-spinner>
                                            <span class="sr-only">Loading...</span>
                                        </span>
                                    </b-button>
                                    </b-col>
                                </b-row>

                                <b-button @click="unlockIssue" v-if="issue.locked" variant="warning">
                                    <span v-if="!unlockButtonLoading">Unlock Issue</span>
                                    <span v-if="unlockButtonLoading">
                                        <b-spinner small></b-spinner>
                                        <span class="sr-only">Loading...</span>
                                    </span>
                                </b-button>
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
                showEdit: false,
                editButtonLoading: false,
                lockButtonLoading: false,
                unlockButtonLoading: false,
                currentLockReason: "off-topic",
                msg: "List Issues"
            };
        },
        created() {
            this.getIssues();
            this.getToken();
        },
        methods: {
            getIssues() {
                this.loaded = false;

                fetch(config.api.url)
                    .then(response => response.json())
                    .then(json => {
                        this.issues = json;
                        this.loaded = true;
                    });
            },
            getToken() {
                this.tokenRetrieved = false;
        
                fetch(config.api.tokenUrl)
                    .then(response => response.json())
                    .then(json => {
                        this.token = json.token;
                        this.tokenRetrieved = true;
                    });
            },
            setCurrentIssue(thisIssue) {
                this.currentId = thisIssue.number;
                this.currentIssue = {
                    title: thisIssue.title,
                    body: thisIssue.body,
                };

                this.showEdit = false;
            },
            isCurrentIssue(issue) {
                return this.currentId == issue.number;
            },
            toggleEdit(){
                this.showEdit = true;
            },
            editIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;
                this.editButtonLoading = true;

                fetch(config.api.url + '/' + this.currentId, {
                    headers: headers,
                    method: 'PATCH',
                    body: JSON.stringify(this.currentIssue)
                })
                .then(response => {
                    this.editButtonLoading = false;
                    this.$bvToast.toast(`Issue edited successfully!`, {
                        title: 'Edit Issue',
                        toaster: "b-toaster-bottom-right",
                        autoHideDelay: 2000,
                        appendToast: false,
                        solid: true,
                        variant: 'success'
                    })
                })
                .catch(error => console.log(error));
            },
            lockIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;
                this.lockButtonLoading = true;

                fetch(config.api.url + '/' + this.currentId + '/lock', {
                    headers: headers,
                    method: 'PUT',
                    body: JSON.stringify({ "locked": true, "active_lock_reason": this.currentLockReason })
                })
                .then(response => {
                    this.lockButtonLoading = false;
                    this.$bvToast.toast(`Issue locked successfully!`, {
                        title: 'Lock Issue',
                        toaster: "b-toaster-bottom-right",
                        autoHideDelay: 2000,
                        appendToast: false,
                        solid: true,
                        variant: 'success'
                    })
                })
                .catch(error => console.log(error));
            },
            unlockIssue() {
                const headers = config.api.headers;
                headers["Authorization"] = "token " + this.token;
                this.unlockButtonLoading = true;
                
                fetch(config.api.url + '/' + this.currentId + '/lock', {
                    headers: headers,
                    method: 'DELETE'
                })
                .then(response => {
                    this.unlockButtonLoading = false;
                    this.$bvToast.toast(`Issue unlocked successfully!`, {
                        title: 'Unlock Issue',
                        toaster: "b-toaster-bottom-right",
                        autoHideDelay: 2000,
                        appendToast: false,
                        solid: true,
                        variant: 'success'
                    })
                })
                .catch(error => console.log(error));
            },
        }
    };

</script>
