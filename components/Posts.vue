<template lang="pug">
div
    div(v-if='$apollo.queries.data.loading') Loading ...
    template(v-else)
        ul.text-left
            li(v-for='post in data.posts', :key='post.id') {{ post.title }} &nbsp;
    v-pagination(
        v-model='currentPage',
        :length='data && data.lastPage',
        circle,
        :total-visible='10'
    )
</template>

<script>
import gql from 'graphql-tag'

const postsQuery = gql`
    query getPosts($page: Int!) {
        posts(page: $page) {
            data {
                id
                title
                content
            }
            paginatorInfo {
                currentPage
                lastPage
            }
        }
    }
`
export default {
    name: 'Posts',
    data() {
        return {
            currentPage: 1,
            data: null,
            lastPage: 1,
        }
    },
    apollo: {
        data() {
            return {
                query: postsQuery,
                variables() {
                    return {
                        page: this.currentPage,
                    }
                },
                update(data) {
                    const posts = data.posts.data
                    const { currentPage, lastPage } = data.posts.paginatorInfo
                    this.currentPage = currentPage
                    this.lastPage = lastPage
                    return { posts, currentPage, lastPage }
                },
            }
        },
    },
}
</script>

<style lang="scss"></style>
