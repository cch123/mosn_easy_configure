<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <!--
    <button v-on:click="counter += 1">connection manager</button>
    <p>The button above has been clicked {{ counter }} times.</p>
    -->
    <hr/>
    <div class="card">
      <h2>Log Configuration</h2>
      <button v-on:click="show_window" name="routes">log settings</button>
    </div>
    <div class="card">
      <h2>Listener Configuration</h2>
      <button v-on:click="show_window" name="routes">listener settings</button>
    </div>
    <div class="card">
      <h2>Admin Configuration</h2>
      <button v-on:click="show_window" name="routes">admin settings</button>
    </div>
    <div class="card">
      <h2>Cluster Configuration</h2>
      <button v-on:click="show_window" name="routes">add cluster rule</button>
    </div>
    <div class="card">
      <h2>Route Configuration</h2>
      <button v-on:click="show_window" name="routes">add route rule</button>
    </div>
    <div class="card">
      <h2>Conn Configuration</h2>
      <button v-on:click="show_window" name="routes">add route rule</button>
    </div>
    <div class="card">
      <h2>Tracing</h2>
      <button v-on:click="show_window" name="tracing">tracing configure</button>
    </div>
    <div class="card">
      <h2>PProf</h2>
      <button v-on:click="show_window" name="pprof">pprof</button>
    </div>
    <hr/>
    <button v-on:click="generate_json">Generate JSON</button>
    <hr/>
    <div>
      <input type="text" id="json_output" :value = "json_data"/>
      <button id="copy_text">Copy</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'MOSN configuration generator',
      counter : 0,
      config : {"x" : 0},
      json_data : ""
    }
  },
  methods : {
    show_window : function(event) {
      alert(event.target.name);
      if (isNaN(this.config[event.target.name]) == true) {
        this.config[event.target.name] = 0;
      }

      this.config[event.target.name] += 1
      alert(this.config[event.target.name])
    },
    generate_json : function(event) {
      this.json_data = JSON.stringify(this.config);
    }
  },
}
</script>

<style>
#copy_text {
  width : 8%;
  height: 30px;
}

#json_output {
  width : 90%;
  height : 30px;
  font-size: 18px;
  padding-left: 12px;
}

.card {
  padding: 10px;
  text-align: left;
  width: 22%;
  display: inline-block;
}

button {
  width : 100%;
  height: 40px;
  font-size : 20px;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
