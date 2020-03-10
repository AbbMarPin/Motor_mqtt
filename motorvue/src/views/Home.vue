<template>
  <v-container fluid>
    <v-row justify="space-around">
      <v-card xl12>
        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">Motor</h3>
          </div>
        </v-card-title>
        <v-card-actions>
          <v-row>
            <v-col cols="12">
              <v-slider v-model="slider" min="0" max="100"></v-slider>
            </v-col>
            <v-col cols="auto">
              <v-switch v-model="onoff" class="ma-2" label="On"></v-switch>
            </v-col>
            <v-switch v-model="Direction" class="ma-2" vertical label="Dierction"></v-switch>
          </v-row>
        </v-card-actions>
        <v-alert type="error" :value="!connected">Not connected!</v-alert>
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
var mqtt = require("mqtt");
export default {
  name: "Home",
  components: {
    // HelloWorld
  },
  data: () => ({
    slider: 0,
    onoff: 1,
    Direction: 1,
    mqtturl: "maqiatto.com",
    connected: false,
    client: undefined
  }),
  created() {
    this.connect();
  },
  mounted() {
    setInterval(() => {
      this.send();
    }, 50);
  },
  methods: {
    send() {
      this.client.publish(
        "martin.pind@abbindustrigymnasium.se/motor",
        this.compose()
      );
    },
    compose() {
      if (this.onoff) {
        this.onoff = 1;
      } else {
        this.onoff = 0;
      }

      if (this.Direction) {
        this.Direction = 1;
      } else {
        this.Direction = 0;
      }
      return `(${this.onoff},${this.Direction},${this.slider})`;
    },
    connect() {
      var mqtt_url = this.mqtturl;
      var url = "mqtt://" + mqtt_url;
      var options = {
        port: 8883,
        clientId:
          "mqttjs_" +
          Math.random()
            .toString(16)
            .substr(2, 8),
        username: "martin.pind@abbindustrigymnasium.se",
        password: "loaldoaldoawldoakdfigvjosgoshnbos"
      };
      console.log("connecting");
      this.client = mqtt.connect(url, options);
      this.client
        .on("connect", function() {
          console.log("connected!!!");
          this.connected = true;
        })
        .on("message", function(topic, message) {
          // message is Buffer
          console.log(message.toString());
        })
        .on("error", function(error) {
          console.log(error);
          this.connected = false;
        })
        .on("close", function(error) {
          console.log(error);
          console.log("closed");
          this.connected = false;
        });
      this.connected = true;
    }
  }
};
</script>
