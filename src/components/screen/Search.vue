<template>
    <div id="search-container">

        <div id="search__user-container">

            <div id="search__user-list">

                <User v-for="user in this.userList"
                      v-bind:key="user"
                      v-bind:userAccess="user" />

                <div v-if="isLoadingUser" class="loading-indicator" />

            </div>

        </div>

    </div>
</template>

<script>
    import User from '../item/User';
    import request from '../../requests';

    async function searchUser() {

        this.$store.commit('resetUserLoadingNumber');

        try {

            const searchUser = await request.user.search(this.query);

            if(searchUser.code === 101) {

                const result = searchUser.result;

                this.addToUserList(result);

            } else console.log(searchUser);

        } catch(error) { console.log(error); }

    }

    function addToUserList(userList) {

        for(const user of userList) {

            this.$store.commit('increaseUserLoadingNumber');
            this.userList.push(user.access);

        }

    }

    function reset() {

        // reset all data
        Object.assign(this.$data, this.$options.data());

        this.searchUser();

    }

    export default {
        props: {
            query: String
        },

        data() {
            return {
                userList: []
            };
        },

        computed: {
            isLoadingUser() { return (this.$store.getters.userLoadingNumber > 0); }
        },

        mounted() {
            this.searchUser();
        },

        watch: {
            query() { this.reset(); }
        },

        methods: {
            searchUser,
            addToUserList,
            reset
        },

        components: {
            User
        }
    };
</script>

<style scoped>
    #search-container {
        flex: 1;

        background-color: #F5F5F5;

        display: flex;
        flex-direction: column;
    }

    #search__user-container {
        flex: 1;
        overflow: auto;
    }

    #search__user-container::-webkit-scrollbar {
        width: 10px;
        background: none;
    }

    #search__user-container::-webkit-scrollbar-thumb {
        background: #253B80;
    }

    #search__user-container::-webkit-scrollbar-track {
        background: none;
    }

    #search__user-list {
        margin: 50px;

        display: flex;
        flex-direction: column;
        align-items: center;
    }
</style>
