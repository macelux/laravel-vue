<template>
    <div>
        <h1>Users</h1>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item"><a class="page-link" href="#">Previous</a></li>
                <li class="page-item"><a class="page-link" href="#">1</a></li>

                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>
        <div class="card card-body" v-bind:key="user.id" v-for="user in users">
            <h6>{{ user.name }}</h6>
            <p>{{ user.email }}</p>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        // return state
        return {
            users: [],
            user: {
                id: '',
                name: '',
                email: ''
            },
            user_id:'',
            pagination: {},
            edit: false
        }
    },

    created() {
        // method runs automatically
        this.fetchUsers();
    },

    methods: {
        fetchUsers(page_url) {
            let vm = this;
            page_url = page_url || 'http://localhost/projectTracker/public/api/users';
            fetch(page_url)
            .then(res => res.json())
            .then(res => {
                this.users = res.data;
                vm.makePagination(res.meta, res.links);
            })
            .catch(err => console.log(err))
        },

        makePagination(meta, links) {
            let pagination = {
                current_page: meta.current_page,
                last_page : meta.last_page,
                next_page_url : links.next,
                prev_page_url : links.prev
            }

            this.pagination = pagination;
        }
    }
}
</script>

