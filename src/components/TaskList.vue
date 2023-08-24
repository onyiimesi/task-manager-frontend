<script>
// @ is an alias to /src
import categoryQuery from "../graphql/queries/Category.gql";
import categoriesQuery from "../graphql/queries/Categories.gql";
import tasksQuery from "../graphql/queries/Tasks.gql";
import tasksByStatus from "../graphql/queries/TasksByStatus.gql";
import { onLogout } from "@/vue-apollo";

export default {
  name: "HomeView",
  components: {},
  data() {
    return {
      // Initialize your apollo data
      categoryQuery,
      categoriesQuery,
      tasksQuery,
      tasksByStatus,
      categories: [],
      query: tasksQuery,
      selectedCategory: "all",
      filteredCategory: "",
      sss: "",
      token: '',
      dueDateThreshold: 1 * 24 * 60 * 60 * 1000
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
        this.query = tasksQuery;
      } else {
        this.query = categoryQuery;
      }

      if (category === 'filter'){

        this.query = tasksByStatus;

        this.sss = this.filteredCategory

      }
      this.selectedCategory = category;
    },

    isTaskNearingDue(task) {
      const dueDate = new Date(task.due_date);
      const timeRemaining = dueDate - new Date();
      return timeRemaining > 0 && timeRemaining < this.dueDateThreshold;
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

    <section class="pt-5 pb-5">
      <div class="container">
        <div class="row">
          <div class="col-md-3">
            <h6>Categories</h6>
            <ApolloQuery :query="categoriesQuery">
              <!-- The result will automatically updated -->
              <template slot-scope="{ result: { data }, isLoading }">
                <!-- Some content -->
                <div v-if="isLoading">Loading...</div>
                <ul v-else>
                  <li class="link-margin">
                    <a href="#" @click.prevent="selectCategory('all')">All</a>
                  </li>
                  <li
                    v-for="category of data.categories"
                    class="link-margin"
                    :key="category.id"
                  >
                    <a href="#" @click.prevent="selectCategory(category.id)">
                      {{ category.id }}. {{ category.name }}
                    </a>
                  </li>
                </ul>
              </template>
            </ApolloQuery>
          </div>

          <div class="col-md-9">
            <div class="mb-3">
              <router-link to="/create-task">
                <button class="btn btn-success btn-sm">Add New Task</button>
              </router-link>
            </div>

            <div class="col-md-2 mb-3">
              <label for="" class="form-label">Filter by</label>
              <select class="form-control" @change="selectCategory('filter')" v-model="filteredCategory">
                <option value="">Choose</option>
                <option value="Not Started">Not Started</option>
                <option value="In Progress">In Progress</option>
                <option value="Working">Working</option>
                <option value="Completed">Completed</option>
              </select>
            </div>
            <div v-if="selectedCategory === 'all'">
              <ApolloQuery :query="query">
                <!-- The result will automatically updated -->
                <template slot-scope="{ result: { data }, isLoading }">
                  <!-- Some content -->
                  <div v-if="isLoading">Loading...</div>
                  <table class="table table-striped table-bordered" v-else>
                    <thead>
                      <tr>
                        <th scope="col">#</th>
                        <th scope="col">Title</th>
                        <th scope="col" style="width: 300px">Description</th>
                        <th scope="col">Due Date</th>
                        <th scope="col">Status </th>
                        <th scope="col">Action</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(task, index) of data.tasks" :key="task.id">
                        <th>{{ index + 1 }}</th>
                        <td>{{ task.title }} <span v-if="isTaskNearingDue(task)" class="badge bg-danger">Due Soon</span></td>
                        <td>{{ task.description }}</td>
                        <td>{{ task.due_date }}</td>
                        <td>{{ task.status }}</td>
                        <td>
                          <router-link :to="`/tasks/${task.id}`"
                            ><button class="btn btn-success btn-sm">
                              View
                            </button></router-link
                          >
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </template>
              </ApolloQuery>
            </div>


            <div v-else-if="selectedCategory === 'filter'">
              <ApolloQuery :query="query" :variables="{ status: sss }">
                <!-- The result will automatically updated -->
                <template slot-scope="{ result: { data }, isLoading }">
                  <!-- Some content -->
                  <div v-if="isLoading">Loading...</div>
                  <table class="table table-striped table-bordered" v-else>
                    <thead>
                      <tr>
                        <th scope="col">#</th>
                        <th scope="col">Title</th>
                        <th scope="col" style="width: 300px">Description</th>
                        <th scope="col">Due Date</th>
                        <th scope="col">Status</th>
                        <th scope="col">Action</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(task, index) of data.tasksByStatus" :key="task.id">
                        <th>{{ index + 1 }}</th>
                        <td>{{ task.title }}</td>
                        <td>{{ task.description }}</td>
                        <td>{{ task.due_date }}</td>
                        <td>{{ task.status }}</td>
                        <td>
                          <router-link :to="`/tasks/${task.id}`"
                            ><button class="btn btn-success btn-sm">
                              View
                            </button></router-link
                          >
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </template>
              </ApolloQuery>
            </div>

            <div v-else>
              <ApolloQuery :query="query" :variables="{ id: selectedCategory }">
                <!-- The result will automatically updated -->
                <template slot-scope="{ result: { data }, isLoading }">
                  <!-- Some content -->
                  <div v-if="isLoading">Loading...</div>
                  <table class="table table-striped table-bordered" v-else>
                    <thead>
                      <tr>
                        <th scope="col">#</th>
                        <th scope="col">Title</th>
                        <th scope="col" style="width: 300px">Description</th>
                        <th scope="col">Due Date</th>
                        <th scope="col">Status</th>
                        <th scope="col">Action</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr
                        v-for="(task, index) of data.category.tasks"
                        :key="task.id"
                      >
                        <th>{{ index + 1 }}</th>
                        <td>{{ task.title }}</td>
                        <td>{{ task.description }}</td>
                        <td>{{ task.due_date }}</td>
                        <td>{{ task.status }}</td>
                        <td>
                          <router-link :to="`/tasks/${task.id}`"
                            ><button class="btn btn-success btn-sm">
                              View
                            </button></router-link
                          >
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </template>
              </ApolloQuery>
            </div> 
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style>
.home {
  width: 100%;
  height: 100%;
  margin: 0 auto;
}

.link-margin {
  margin-right: 24px;
}

.home header {
  width: 100%;
  height: 100%;
  background-color: black;
}

.home header h3 {
  color: #fff;
  font-size: 1.5rem;
}

.home section {
  margin: 20px 0 0 0;
}

a{
  text-decoration: none !important;
}
</style>
