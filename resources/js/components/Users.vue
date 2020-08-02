<template>
    <div>
        <h1>Users</h1>
        <form @submit.prevent="addUser" class="mb-3">
            <div class="form-group">
                <input type="text" class="form-control" placeholder="name"
                 v-model="user.name" >
            </div>
             <div class="form-group">
                <input type="text" class="form-control" placeholder="email"
                 v-model="user.email" >
            </div>
            <button type="submit" class="btn btn-light btn-block">submit</button>
        </form>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{disabled: !pagination.prev_page_url}]"
                    class="page-item">
                    <a class="page-link" href="#" @click="fetchUsers(pagination.prev_page_url)">Previous</a></li>

                    <li class="page-item disabled">
                        <a class="page-link text-dark" href="#">
                            Page {{ pagination.current_page }} of {{ pagination.last_page}}
                        </a>
                    </li>

                <li v-bind:class="[{disabled: !pagination.next_page_url}]"
                    class="page-item">
                    <a class="page-link" href="#" @click="fetchUsers(pagination.next_page_url)">Next</a></li>
            </ul>
        </nav>
        <div class="card card-body" v-bind:key="user.id" v-for="user in users">
            <h6>{{ user.name }}</h6>
            <p>{{ user.email }}</p>
            <hr>
            <button @click="editUser(user)" class="btn btn-warning">edit</button>
            <button @click="deleteUser(user.id)" class="btn btn-danger">Delete</button>
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
        // fetch user
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
        // make pagination
        makePagination(meta, links) {
            let pagination = {
                current_page: meta.current_page,
                last_page : meta.last_page,
                next_page_url : links.next,
                prev_page_url : links.prev
            }

            this.pagination = pagination;
        },

        // delete user
        deleteUser(id) {
            if((confirm('Are you sure?'))){
                fetch(`http://localhost/projectTracker/public/api/user/${id}`, {
                    method: 'delete'
                })
                .then(res => res.json())
                .then(data => {
                    alert('User removed');
                    this.fetchUsers();
                })
            }
        },

        addUser() {
            if(this.edit === false){
                // add
                fetch('http://localhost/projectTracker/public/api/user',{
                    method: 'post',
                    body: JSON.stringify(this.user),
                    headers: {
                        'content-type' : 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.user.title = '';
                    this.user.email = '';
                    alert('User added');
                    this.fetchUsers();
                });

            } else {
                // update

                 fetch('http://localhost/projectTracker/public/api/user',{
                    method: 'put',
                    body: JSON.stringify(this.user),
                    headers: {
                        'content-type' : 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.user.title = '';
                    this.user.email = '';
                    alert('User Updated');
                    this.fetchUsers();
                });
            }
        },
        editUser(user){
            this.edit = true;
            this.user.id = user.id;
            this.user.user_id = user.id;
            this.user.name = user.name ;
            this.user.email = user.email ;
        }
    }
}
</script>

