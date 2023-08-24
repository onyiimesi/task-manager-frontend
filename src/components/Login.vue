<script>
// @ is an alias to /src
import Login from "../graphql/mutations/Login.gql";
import { onLogin } from "@/vue-apollo";

export default {
  name: "HomeView",
  components: {},
  data() {
    return {
      // Initialize your apollo data
      email: '',
      password: ''
    };
  },

  methods: {
    Login () {

        this.$apollo.mutate({
            // Query
            mutation: Login,
            // Parameters
            variables: {
                email: this.email,
                password: this.password
            }
            
        }).then((data) => {
            this.$toast.success("Login Successful");
            this.email = '';
            this.password = '';
            onLogin(this.$apollo.provider.defaultClient, data.data.loginUser.token)
            this.$router.push('/tasks');
            }).catch((error) => {
            // console.error(error)
            this.$toast.error(error);
        })
        
    }
  }
};
</script>

<template>
  <div class="home">
    <header>
      <div class="container">
        <div class="row">
          <div class="col-md-12 pt-2 pb-2">
            <router-link to="/"><h3>Task Manager</h3></router-link>
          </div>
        </div>
      </div>
    </header>


    <section class="py-5">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-md-6 shadow-sm p-4 rounded">
            <h4 class="pb-3">Login</h4>
            <form @submit.prevent="Login">
                <div class="mb-3">
                    <label for="" class="form-label">Email Address</label>
                    <input type="text" class="form-control" v-model="email">
                </div>
                <div class="mb-3">
                    <label for="" class="form-label">Password</label>
                    <input type="password" class="form-control" v-model="password">
                </div>
                <button type="submit" class="btn btn-primary mt-3">Login</button>
                <p class="pt-3">
                    Don't have an account? <router-link to="/register">Register</router-link>
                </p>
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