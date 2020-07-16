<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <!--
    <button v-on:click="counter += 1">connection manager</button>
    <p>The button above has been clicked {{ counter }} times.</p>
    -->
    <hr />
    <div class="card">
      <h2>Log</h2>
      <label>Log Level</label>
      <input type="text" :value="log.level" />
      <br />
      <label>Log Path</label>
      <input type="text" :value="log.path" />
    </div>
    <div class="card">
      <h2>Admin</h2>
      <label>IP</label>
      <input type="text" :value="admin.ip" />
      <br />
      <label>Port</label>
      <input type="text" :value="admin.port" />
    </div>
    <div class="card">
      <h2>Tracing</h2>
      <label>Enable</label>
      <input type="text" :value="tracing.enable" />
      <br />
      <label>Driver</label>
      <input type="text" :value="tracing.driver" />
    </div>
    <div class="card">
      <h2>PProf</h2>
      <label>Enable</label>
      <input type="text" :value="pprof.debug" />
      <br />
      <label>Port</label>
      <input type="text" :value="pprof.port_value" />
    </div>
    <hr />
    <h2>stream direction : listener -> router -> cluster</h2>
    <div style="width:100%">
      <div class="card_wide">
        <h2>Cluster</h2>
        <label>name</label>
        <input type="text" :value="current_cluster.name" />
        <label>type</label>
        <input type="text" :value="current_cluster.type" />
        <label>lb_type</label>
        <input type="text" :value="current_cluster.lb_type" />
        <br />
        <label>max_request_per_conn</label>
        <input type="text" :value="current_cluster.max_request_per_conn" />
        <label>conn_buffer_limit_bytes</label>
        <input type="text" :value="current_cluster.conn_buffer_limit_bytes" />
        <br />
        <label>address list:</label>
        <br />
        <input
          class="addr_box"
          type="text"
          v-for="item in current_cluster.hosts"
          :key="item.address"
          :value="item.address"
        />
        <a v-on:click="add_addr_to_cluster"> add </a>
      </div>
      <div class="card_wide">
        <h2>Router</h2>
        <label>name</label>
        <input type="text" :value="current_router.router_config_name" />
        <br />
        <a> add </a>
      </div>
      <div class="card_wide">
        <h2>Listener</h2>
        <label>name</label>
        <input type="text" :value="current_listener.name" /> <br/>
        <div v-for="chain in current_listener.filter_chains" :key="chain.index">
          <div v-for="filter in chain.filter" :key="filter.index">
            <label>filter_type</label><input type="text" :value="filter.type"/> <br/>
            <label>downstream_protocol</label><input type="text" :value="filter.config.downstream_protocol"/> <br/>
            <label>upstream_protocol</label><input type="text" :value="filter.config.upstream_protocol"/> <br/>
            <label>router name</label><input type="text" :value="filter.config.router_config_name"/> <br/>
          </div>
        </div>
        <a> add </a>
      </div>
    </div>
    <hr />
    <button v-on:click="generate_json">Generate JSON</button>
    <hr />
    <div>
      <input type="text" id="json_output" :value="json_data" />
      <button id="copy_text">Copy</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      msg: "MOSN configuration generator",
      json_data: "",
      log: {
        level: "INFO",
        path: "/tmp/test.log"
      },
      admin: {
        ip: "0.0.0.0",
        port: 12345
      },
      tracing: {
        enable: false,
        driver: "SOFATracer"
      },
      pprof: {
        debug: true,
        port_value: 34092
      },
      current_listener: {
        name : "serverListener",
        address : "127.0.0.1:2045",
        bind_port : true,
        filter_chains : [
          {
            filter : [
              {
                type : "proxy",
                config : {
                  downstream_protocol: "Http1",
									upstream_protocol: "Http1",
									router_config_name:"serverRouter"
                }
              },
            ]
          },
        ],
      },
      current_router: {
        router_config_name: "serverRouter",
        virtual_hosts: []
      },
      current_cluster: {
        name: "serverCluster",
        type: "SIMPLE",
        lb_type: "LB_RANDOM",
        max_request_per_conn: 1024,
        conn_buffer_limit_bytes: 32768,
        hosts: [{ address: "127.0.0.1:8080" }]
      }
    };
  },
  methods: {
    add_addr_to_cluster : function(event) {
      this.current_cluster.hosts.push({address : "127.0.0.1:8080"})
    },
    confirm_log: function(event) {
      alert(this.log.path);
    },
    show_window: function(event) {
      alert(event.target.name);
    },
    generate_json: function(event) {
      var x = {
        log : this.log,
        admin : this.admin,
        tracing : this.tracing,
        pprof : this.pprof,
        listeners : this.current_cluster,
        cluster : this.current_cluster,
        router : this.current_router,
      }
      this.json_data = JSON.stringify(x);
    }
  }
};
</script>

<style>
.addr_box {
  display: block;
}

#copy_text {
  width: 8%;
  height: 30px;
}

#json_output {
  width: 90%;
  height: 30px;
  font-size: 18px;
  padding-left: 12px;
}

.card_wide {
  width: 100%;
  height: 200px;
  display: inline-block;
}

.card {
  height: 160px;
  padding: 10px;
  text-align: left;
  width: 22%;
  display: inline-block;
}

button {
  width: 100%;
  height: 40px;
  font-size: 20px;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

h1,
h2 {
  font-weight: normal;
  text-align:center;
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
