Steps to install
* Clone git clone https://github.com/onyiimesi/task-manager-frontend.git
* Run yarn install
* Open the folder in an editor and navigate to src/vue-apollo.js
* Look for this const httpEndpoint =
  process.env.VUE_APP_GRAPHQL_HTTP || "URL";
* Change the URL to the url of the backend e.g http://127.0.0.1:8000/graphql
* Run yarn run serve to start the application.


# taskmanager

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
