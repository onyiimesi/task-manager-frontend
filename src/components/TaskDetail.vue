<script>
// @ is an alias to /src
import taskQuery from "../graphql/queries/Task.gql";
import deleteQuery from "../graphql/mutations/DeleteTask.gql";
import { onLogout } from "@/vue-apollo";

export default {
  name: "HomeView",
  components: {},
  data() {
    return {
      // Initialize your apollo data
      taskQuery,
      categories: [],
      query: taskQuery,
      selectedCategory: "all",
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
    selectCategory(category) {
      if (category === "all") {
        this.query = taskQuery;
      } else {
        this.query = categoryQuery;
      }
      this.selectedCategory = category;
    },

    deleteTask () {
      this.$apollo.mutate({
        mutation: deleteQuery,
        variables: {
          id: this.$route.params.id,
        }
      }).then((data) => {

        this.$toast.success("Deleted Successfully");
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
  },
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


    <section>
      <div class="container">
        <div class="row">
          <div class="col-md-12">
            <ApolloQuery :query="taskQuery" :variables="{id: $route.params.id}">
              <!-- The result will automatically updated -->
              <template slot-scope="{ result: { data }, isLoading }">
                <!-- Some content -->
                <div v-if="isLoading">Loading...</div>
                <div v-else>
                  <h2>{{ data.task.title }}</h2>
                  <p>{{ data.task.description }}</p>
                  <span>{{ data.task.due_date }}</span>
                  <p>{{ data.task.status }}</p>

                  <router-link :to="`${data.task.id}/edit`"><button class="btn btn-success btn-sm me-3">Edit</button></router-link>

                  <button class="btn btn-danger btn-sm" @click.prevent="deleteTask">Delete</button>
                </div>
              </template>
            </ApolloQuery>
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