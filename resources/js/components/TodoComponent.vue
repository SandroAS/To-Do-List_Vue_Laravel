<template>
    <div class="w-50">
        <form @submit.prevent="saveData">
            <div class="input-group mb-3 w-100">
                <input @keydown="form.errors.clear('title')" v-model="form.title" :class="{'is-invalid' : form.errors.has('title')}" type="text" class="form-control form-control-lg" aria-label="Recipient's username" aria-describedby="button-addon2">
                <div class="input-group-append">
                    <button class="btn btn-success" type="submit" id="button-addon2">Add this to your list</button>
                </div>
            </div>
            <span class="text-danger pt-3 pb-3" style="font-size:20px;" v-if="form.errors.has('title')" v-text="form.errors.get('title')"></span>
        </form>
        <div class="w-100">
            <ul class="list-group w-100">
                <li v-for="todo in todos" :key="todo.id" class="list-group-item d-flex flex-row">
                    <input @click="updateData(todo)" v-model="todo.completed" class="form-check-input me-1" style="margin-left: -8px;" type="checkbox">
                    <p class="mb-0 pl-3">{{ todo.title }}</p>
                    <button @click="deleteData(todo)" type="button" class="btn btn-danger btn-sm ml-auto">Delete</button>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                todos: "",
                completed: "",
                form: new Form({
                    title: ''
                })
            }
        },

        methods: {
            getTodos(){
                axios.get('/api/todo').then((res) => {
                    this.todos = res.data;
                }).catch((error) => {
                    console.log(error);
                })
            },

            saveData(){
                let data = new FormData();
                data.append('title', this.form.title)
                data.append('completed', 0)
                axios.post('/api/todo', data).then((res) => {
                    this.form.reset();
                    this.getTodos()
                }).catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            },

            updateData(item){
                let completed;
                if(item.completed == 0 || item.completed == false) {
                    completed = 1;
                } else {
                    completed = 0;
                }
                let data = item;
                data.completed = completed;
                console.log(data)
                axios.put(`/api/todo/${item.id}`, data).then((res) => {
                    console.log(res);
                }).catch((error) => {
                    console.log(error);
                })
            },

            deleteData(item){
                axios.delete(`/api/todo/${item.id}`).then((res) => {
                    console.log(res)
                    this.getTodos()
                }).catch((error) => {
                    console.log(error)
                })
            }
        },

        mounted() {
            this.getTodos();
        }
    }
</script>
