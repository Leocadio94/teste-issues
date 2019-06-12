<template>
    <div class="list-issues">
        <h1>{{ msg }}</h1>
        <ul>
            <li v-for="issue in issues">
                <img :src="issue.user.avatar_url" width="20px" height="20px">
                ({{ issue.state }}) - {{ issue.title }}- {{issue.body}}
                <button @click="setCurrentIssue(issue)">Edit Issue</button>
            </li>
        </ul>
        <div>
            <input v-model.trim="currentIssue.title" placeholder="Name" type="text">
            <textarea v-model.trim="currentIssue.body" placeholder="Description"></textarea>

            <button @click="editIssue">Edit Issue</button>
            <button @click="lockIssue">Lock Issue</button>
            <button @click="unlockIssue">Unlock Issue</button>
        </div>
    </div>
</template>

<script>
    export default {
        name: "ListIssues",
        data() {
            return {
                issues: [],
                currentId: 0,
                currentIssue: {},
                msg: "List Issues"
            };
        },
        created() {
            fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues")
                .then(response => response.json())
                .then(json => {
                    this.issues = json;
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
            editIssue() {
                fetch("https://api.github.com/repos/Leocadio94/teste-issues/issues/" + this.currentId, {
                    headers: { 
                        "Content-Type": "application/json; charset=utf-8",
                        "Authorization": "token a1b0f5582ec7a3611e5da437fcde683c89197d45"
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
                        "Authorization": "token a1b0f5582ec7a3611e5da437fcde683c89197d45"
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
                        "Authorization": "token a1b0f5582ec7a3611e5da437fcde683c89197d45"
                    },
                    method: 'DELETE'
                })
                .then(response => console.log(response))
                .catch(error => console.log(error));
            },
        }
    };

</script>
