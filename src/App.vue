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

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
