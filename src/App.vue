<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <!--
    <button v-on:click="counter += 1">connection manager</button>
    <p>The button above has been clicked {{ counter }} times.</p>
    -->
    <hr/>
    <div class="card">
      <h2>Log</h2>
      <button v-on:click="show_window" name="log">confirm</button>
      <label>Log Level</label> <input type="text" :value = "log.level" /> <br/>
      <label>Log Path</label> <input type="text" :value = "log.path"/>
    </div>
    <div class="card">
      <h2>Admin</h2>
      <button v-on:click="show_window" name="admin">confirm</button>
      <label>IP</label> <input type="text" :value = "admin.ip" /> <br/>
      <label>Port</label> <input type="text" :value = "admin.port"/>
    </div>
    <div class="card">
      <h2>Tracing</h2>
      <button v-on:click="show_window" name="tracing">settings</button>
      <label>Enable</label> <input type="text" :value = "tracing.enable" /> <br/>
      <label>Driver</label> <input type="text" :value = "tracing.driver"/>
    </div>
    <div class="card">
      <h2>PProf</h2>
      <button v-on:click="show_window" name="pprof">settings</button>
      <label>Enable</label> <input type="text" :value = "pprof.debug" /> <br/>
      <label>Port</label> <input type="text" :value = "pprof.port_value"/>
    </div>
    <h2> stream direction : listener -> router -> cluster</h2>
    <div class="card">
      <h2>Listener</h2>
      <button v-on:click="show_window" name="listener">settings</button>
      <ul></ul>
    </div>
    <div class="card">
      <h2>Route</h2>
      <button v-on:click="show_window" name="route">settings</button>
      <ul>
      </ul>
    </div>
    <div class="card">
      <h2>Cluster</h2>
      <button v-on:click="show_window" name="cluster">settings</button>
      <ul>
      </ul>
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
      config : {},
      json_data : "",
      log : {
        level : "INFO",
        path : "/tmp/test.log"
      },
      admin : {
        ip : "0.0.0.0",
        port : 12345
      },
      tracing : {
        enable : true,
        driver: "SOFATracer" 
      },
      pprof : {
        debug : true,
        port_value : 34092
      },
    }
  },
  methods : {
    confirm_log : function(event) {
    },
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
  height: 160px;
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
