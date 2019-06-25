<template>
    <div class="header">
        <b-navbar type="dark" variant="primary">
            <b-navbar-brand href="#">{{ title }}</b-navbar-brand>
        </b-navbar>
    </div>
</template>

<script>
    export default {
        name: "Header",
        data() {
            return {
                title: "Github Issues"
            };
        }
    };
</script>
