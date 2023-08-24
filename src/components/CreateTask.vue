<script>
// @ is an alias to /src
import categoriesQuery from "../graphql/queries/Categories.gql";
import AddTask from "../graphql/mutations/AddTask.gql";
import { onLogout } from "@/vue-apollo";

export default {
  name: "HomeView",
  components: {},
  data() {
    return {
      // Initialize your apollo data
      categoriesQuery,
      title: '',
      description: '',
      due_date: '',
      status: '',
      category_id: 1,
      token: ''
    };
  },

  async mounted(){
    const token = localStorage.getItem('apollo-token');
    if(!token){
      this.$router.push('/');
    }
  },

  methods: {
    addTask () {

        this.$apollo.mutate({
            // Query
            mutation: AddTask,
            // Parameters
            variables: {
                title: this.title,
                description: this.description,
                due_date: this.due_date,
                status: this.status,
                category_id: this.category_id
            }
            
        }).then((data) => {
            this.$toast.success("Created Successfully");
            this.title = '';
            this.description = '';
            this.due_date = '';
            this.status = '';
            this.category_id = '';
            this.$router.push('/');
            }).catch((error) => {
            // console.error(error)
            this.$toast.error(error);
        })
    },

    logout(){
      onLogout(this.$apollo.provider.defaultClient);
      this.$toast.success("Logged Out Successfully");
      this.$router.push('/');
    }
  }
};
</script>

<template>
  <div class="home">
    <header>
      <div class="container">
        <div class="row">
          <div class="col-md-6 pt-2 pb-2">
            <router-link to="/tasks"><h3>Task Manager</h3></router-link>
          </div>
          <div class="col-md-6 pt-2 pb-2">
            <div class="text-end">
              <button class="btn btn-danger" @click="logout">Logout</button>
            </div>
          </div>
        </div>
      </div>
    </header>


    <section class="py-5">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-md-6 shadow-sm p-4 rounded">
            <h4 class="pb-3">Add New Task</h4>
            <form @submit.prevent="addTask">
                <div class="mb-3">
                    <label for="" class="form-label">Category</label>
                    <ApolloQuery :query="categoriesQuery">
                        <!-- The result will automatically updated -->
                        <template slot-scope="{ result: { data }, isLoading }">
                            <!-- Some content -->
                            <select
                                class="form-control"
                                v-model="category_id"
                            >
                                <option v-for="category of data.categories"
                                :key="category.id" :value="category.id">
                                    {{ category.name }}
                                </option>
                            </select>
                        </template>
                    </ApolloQuery>
                </div>
                <div class="mb-3">
                    <label for="" class="form-label">Title</label>
                    <input type="text" class="form-control" v-model="title">
                </div>
                <div class="mb-3">
                    <label for="" class="form-label">Description</label>
                    <textarea name="" class="form-control" id="" cols="30" rows="5" v-model="description"></textarea>
                </div>
                <div class="mb-3">
                    <label for="" class="form-label">Due Date</label>
                    <input type="date" class="form-control" v-model="due_date">
                </div>
                <div class="mb-3">
                    <label for="" class="form-label">Status</label>
                    <select name="" class="form-control" id="" v-model="status">
                        <option value="">Choose</option>
                        <option value="Not Started">Not Started</option>
                        <option value="In Progress">In Progress</option>
                        <option value="Completed">Completed</option>
                    </select>
                </div>
                <button type="submit" class="btn btn-primary mt-3">Add Task</button>
            </form>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style>
  .home header {
    width: 100%;
    height: 100%;
    background-color: black;
  }

  .home header h3 {
    color: #fff;
    font-size: 1.5rem;
  }

  a{
    text-decoration: none !important;
  }
</style>