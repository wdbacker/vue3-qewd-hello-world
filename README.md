# vue3-qewd-hello-world

## Generate your app skeleton
```bash
vue create vue3-qewd-hello-world
```
Choose the Vue.js 3.x defaults when asked for

## Project setup
```bash
npm install
# to enable use of WebSockets for QEWD.js interactive apps
npm install socket.io-client qewd-client
```

### !!! Important !!!

- currently, qewd-client is not published yet on npm, so just copy qewd-client.js to your app's node_modules dir
- for use with Vue.js apps, use my slightly modified qewd-client.js fork on https://github.com/wdbacker/qewd-client

### File changes to a fresh Vue.js 3.x app skeleton to integrate with QEWD.js interactive apps (using WebSockets)

- in `src/main.js`:
```javascript
import { createApp } from 'vue'
import App from './App.vue'

// import QEWD from the qewd-client
import { QEWD } from 'qewd-client'

// create the Vue App instance
const app = createApp(App)
// add QEWD as a global property
// now you can use QEWD in all your app components as `this.$qewd.reply(...)`
app.config.globalProperties.$qewd = QEWD
// mount the Vue.js app as usual
app.mount('#app')
```
- in `src/App.vue`:
```html
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <!-- show your app's main component when QEWD.js's websocket is ready -->
  <HelloWorld v-if="qewdReady" msg="Welcome to Your Vue.js App"/>
  <!-- during startup, show a startup indicator -->
  <h1 v-else>Starting QEWD.js ...</h1>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
// import the socket.io client into our App component
import io from 'socket.io-client'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data () {
    return {
      // define a reactive property set to true when QEWD's websocket is registered
      qewdReady: false
    }
  },
  created () {
    // preserve Vue.js's this pointer
    let self = this
    /*
      create an event handler invoked when QEWD's connection is registered/ready
    */
    this.$qewd.on('ewd-registered', function() {
      // Your QEWD environment is ready, set the qewdReady data property to true
      self.qewdReady = true
      // optionally turn on logging to the console
      self.$qewd.log = true
    });
    /*
      start QEWD.js with these required params:
      - application: QEWD's application name
      - io: the imported websocket client module
      - url: the url of your QEWD.js server (hardcoded in this example,
        the url can be passed in too using a process.env.QEWD_URL parameter)

      *** important: by default, a Vue.js app will run it's development server on localhost:8080 
      (this is the same port as QEWD.js's default port 8080)
      you'll *need* to change the port to e.g. 8090 in QEWD.js's config
      to make it work with a Vue.js app!
    */
    this.$qewd.start({
      application: 'hello-world',
      io,
      url: 'http://localhost:8090'
    })
  }
}
</script>
```
- in `src/components/HelloWorld.vue`:
```html
<template>
  <div class="hello">
    ...
  </div>
  <hr>
  <p>message from QEWD.js: {{ qewdMessage }}</p>
  <button @click="sendMessage">Send message to QEWD.js</button>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      // add a reactive property to hold a message coming back from QEWD.js
      qewdMessage: ''
    }
  },
  methods: {
    sendMessage() {
      // preserve Vue.js's this for use in QEWD.js's reply
      let self = this
      // add a debugger statement if you want to debug your Vue app
      /* eslint-disable no-debugger, no-console */
      debugger
      // send a test 'hello-world' message to QEWD.js
      this.$qewd.reply({
        type: 'hello-world'
      }).then(response => {
        debugger
        // log QEWD.js's response on the console
        console.log(response)
        // show an error message in the app
        self.qewdMessage = response.message.error || ''
      }) 
    }
  }
}
</script>
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

### Debugging with VSCode

See the recipe at [Vue.js debugging in Chrome and VS Code](https://github.com/microsoft/vscode-recipes/tree/master/vuejs-cli)