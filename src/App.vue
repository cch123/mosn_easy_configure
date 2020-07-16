<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <hr />
    <div class="card">
      <h2>Log</h2>
      <h4>Log Level</h4>
      <input type="text" :value="log.level" />
      <br />
      <h4>Log Path</h4>
      <input type="text" :value="log.path" />
    </div>
    <div class="card">
      <h2>Admin</h2>
      <h4>IP</h4>
      <input type="text" :value="admin.ip" />
      <br />
      <h4>Port</h4>
      <input type="text" :value="admin.port" />
    </div>
    <div class="card">
      <h2>Tracing</h2>
      <h4>Enable</h4>
      <input type="text" :value="tracing.enable" />
      <br />
      <h4>Driver</h4>
      <input type="text" :value="tracing.driver" />
    </div>
    <div class="card">
      <h2>PProf</h2>
      <h4>Enable</h4>
      <input type="text" :value="pprof.debug" />
      <br />
      <h4>Port</h4>
      <input type="text" :value="pprof.port_value" />
    </div>
    <hr />

    <h2>stream direction : listener -> router -> cluster</h2>

    <!-- cluster start -->
    <div class="card_wide">
      <h2>Cluster</h2>
      <table v-for="(cluster,index) in clusters" :key="index" style="border:1px solid gray">
        <tr>
          <td>
            <h4>name</h4>
            <input type="text" :value="cluster.name" />
          </td>
          <td>
            <h4>type</h4>
            <input type="text" :value="cluster.type" />
          </td>
          <td>
            <h4>lb_type</h4>
            <input type="text" :value="cluster.lb_type" />
          </td>
          <td>
            <h4>max_request_per_conn</h4>
            <input type="text" :value="cluster.max_request_per_conn" />
          </td>
          <td>
            <h4>conn_buffer_limit_bytes</h4>
            <input type="text" :value="cluster.conn_buffer_limit_bytes" />
          </td>
        </tr>
        <tr>
          <td>
            <h4>address list:</h4>
            <input
              class="addr_box"
              type="text"
              v-for="item in clusters.hosts"
              :key="item.address"
              :value="item.address"
            />
            <a v-on:click="add_addr_to_cluster">add</a>
          </td>
        </tr>
      </table>
    </div>
    <!-- cluster end -->

    <!-- router start -->
    <div class="card_wide">
      <h2>Router</h2>
      <table v-for="(router, index) in routers" :key="index" style="border:1px solid gray">
        <tr>
          <td>
            <h4>name</h4>
            <input type="text" :value="router.router_config_name" />
          </td>
          <td>
            <h4>routers</h4>
            <div v-for="(vhost, index) in router.virtual_hosts" :key="index" class="vhost_block">
              <label>host name</label>
              <input type="text" :value="vhost.name" />
              <label>host domain</label>
              <input type="text" :value="vhost.domains" />
              <div v-for="(r, index) in vhost.routers" :key="index">
                <label>match</label>
                <input type="text" :value="r.match" />
                <label>route</label>
                <input type="text" :value="r.route.cluster_name" />
              </div>
            </div>
          </td>
        </tr>
      </table>
    </div>
    <!-- router end -->

    <!-- listener start -->
    <div class="card_wide">
      <h2>Listener</h2>
      <div v-for="listener in listeners" :key="listener.name" style="border:1px solid gray">
        <table>
          <tr>
            <td>
              <h4>name</h4>
              <input type="text" :value="listener.name" />
            </td>
            <td>
              <h4>address</h4>
              <input type="text" :value="listener.address" />
            </td>
            <td>
              <h4>bind port</h4>
              <input type="text" :value="listener.bind_port" />
            </td>
          </tr>
        </table>
        <table>
          <tr v-for="(chain, index) in listener.filter_chains" :key="index">
            <div v-for="filter in chain.filter" :key="filter.index" style="border:1px solid gray">
              <td>
                <h4>filter_type</h4>
                <input type="text" :value="filter.type" />
              </td>
              <td>
                <h4>downstream_protocol</h4>
                <input type="text" :value="filter.config.downstream_protocol" />
              </td>
              <td>
                <h4>upstream_protocol</h4>
                <input type="text" :value="filter.config.upstream_protocol" />
              </td>
              <td>
                <h4>router name</h4>
                <input type="text" :value="filter.config.router_config_name" />
              </td>
            </div>
          </tr>
        </table>
      </div>
    </div>
    <hr />
    <!-- listener end -->

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
      listeners: [
        {
          name: "serverListener",
          address: "127.0.0.1:2045",
          bind_port: true,
          filter_chains: [
            {
              filter: [
                {
                  type: "proxy",
                  config: {
                    downstream_protocol: "Http1",
                    upstream_protocol: "Http1",
                    router_config_name: "serverRouter"
                  }
                }
              ]
            }
          ]
        }
      ],
      routers: [
        {
          router_config_name: "serverRouter",
          virtual_hosts: [
            {
              name: "serverHost",
              domains: '["*"]',
              routers: [
                {
                  match: '{prefix : "/"}', // 还可能有其它结果
                  route: { cluster_name: "serverCluster" }
                }
              ]
            }
          ]
        }
      ],
      clusters: [{
        name: "serverCluster",
        type: "SIMPLE",
        lb_type: "LB_RANDOM",
        max_request_per_conn: 1024,
        conn_buffer_limit_bytes: 32768,
        hosts: [{ address: "127.0.0.1:8080" }]
      }]
    };
  },
  methods: {
    add_addr_to_cluster: function(event) {
      this.clusters.hosts.push({ address: "127.0.0.1:8080" });
    },
    show_window: function(event) {
      alert(event.target.name);
    },
    generate_json: function(event) {
      var x = {
        log: this.log,
        admin: this.admin,
        tracing: this.tracing,
        pprof: this.pprof,
        listeners: this.clusters,
        cluster: this.clusters,
        router: this.current_router
      };
      this.json_data = JSON.stringify(x);
    }
  }
};
</script>

<style>
.vhost_block {
  border: 1px solid gray;
  padding: 3px;
}

td {
  padding: 10px;
}

h4 {
  font-size: 20px;
}

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
  height: auto;
  display: inline-block;
  border: 1px solid gray;
  margin-top: 20px;
  padding: 10px;
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
  margin-top: 0px;
  padding: 15px;
}

h1,
h2 {
  font-weight: normal;
  text-align: center;
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
