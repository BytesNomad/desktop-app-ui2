<template>
  <div id="main">
    <button class="serverSelectBtn" v-on:click="showServersList()">
      <div align="left">
        <div class="small_text">
          {{
            this.isExitServer
              ? "Exit server"
              : isConnected
              ? "Connected to"
              : isConnecting
              ? "Connecting to ..."
              : "Connect to"
          }}
        </div>
        <div style="height: 4px" />
        <serverNameControl
          class="serverName"
          size="large"
          :server="this.server"
          :isFastestServer="
            isDisconnected && $store.getters['settings/isFastestServer']
          "
          :isRandomServer="isRandomServer"
          :isShowPingPicture="
            !isDisconnected ||
              (isDisconnected &&
                !($store.getters['settings/isFastestServer'] || isRandomServer))
          "
        />
      </div>

      <div class="arrowRightSimple"></div>
    </button>
  </div>
</template>

<script>
import serverNameControl from "@/components/controls/control-server-name.vue";
import { VpnStateEnum } from "@/store/types";

export default {
  props: ["onShowServersPressed", "isExitServer"],
  components: {
    serverNameControl
  },
  computed: {
    server: function() {
      return this.isExitServer
        ? this.$store.state.settings.serverExit
        : this.$store.state.settings.serverEntry;
    },
    isConnected: function() {
      return (
        this.$store.state.vpnState.connectionState === VpnStateEnum.CONNECTED
      );
    },
    isConnecting: function() {
      return this.$store.getters["vpnState/isConnecting"];
    },
    isDisconnected: function() {
      return (
        this.$store.state.vpnState.connectionState === VpnStateEnum.DISCONNECTED
      );
    },
    isRandomServer: function() {
      if (!this.isDisconnected) return false;
      return this.isExitServer
        ? this.$store.getters["settings/isRandomExitServer"]
        : this.$store.getters["settings/isRandomServer"];
    }
  },
  methods: {
    showServersList() {
      if (this.onShowServersPressed != null)
        this.onShowServersPressed(this.isExitServer);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/components/scss/constants";

#main {
  @extend .left_panel_block;
}

.small_text {
  font-size: 14px;
  line-height: 17px;
  letter-spacing: -0.3px;
  color: $base-text-color-details;
}

.serverSelectBtn {
  padding: 0px;
  border: none;
  background-color: inherit;
  outline-width: 0;
  cursor: pointer;

  display: flex;
  justify-content: space-between;
  align-items: center;

  height: 82px;
  width: 100%;
}

.serverName {
  max-width: 270px;
}
</style>
