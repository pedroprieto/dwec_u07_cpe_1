
<template>
    <div>
            <a class="d-flex justify-content-between list-group-item list-group-item-action" :href="repo.html_url" :title="repo.description">
                {{repo.full_name}}
                <span class="badge badge-primary badge-pill">{{repo.forks_count}} &#9282;</span>
            </a>
    </div>
</template>

<script>
export default {
    name: 'GitHubRepo',
    props: [
        'repo'
    ],
}
</script>

<style scoped>
</style>
