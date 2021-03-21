<template lang="pug">
div
    div(v-if='$apollo.queries.data.loading') Loading ...
    ul.text-left(v-else)
        li(v-for='user in data.users', :key='user.id')
            b {{ user.name }} &nbsp;
            span <{{ user.email }}>
    v-pagination(
        v-model='currentPage',
        :length='data && data.lastPage',
        circle,
        :total-visible='10'
    )
</template>

<script>
import gql from 'graphql-tag'

const usersQuery = gql`
    query getUsers($page: Int!) {
        users(page: $page) {
            data {
                id
                name
                email
            }
            paginatorInfo {
                currentPage
                lastPage
            }
        }
    }
`

export default {
    name: 'Users',
    data() {
        return {
            currentPage: 1,
            lastPage: 1,
        }
    },
    apollo: {
        data() {
            return {
                query: usersQuery,
                variables() {
                    return {
                        page: this.currentPage,
                    }
                },
                update(data) {
                    const users = data.users.data
                    const { currentPage, lastPage } = data.users.paginatorInfo
                    this.currentPage = currentPage
                    this.lastPage = lastPage
                    return { users, currentPage, lastPage }
                },
            }
        },
    },
}
</script>

<style lang="scss"></style>
