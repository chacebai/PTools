<template>
  <div class="hello">
    <h1>Hello</h1>
    <div id="xterm" class="xterm" />
  </div>
</template>

<script>
import "xterm/css/xterm.css";
import { Terminal } from "xterm";
// xterm.js的插件，使终端的尺寸适合包含元素。
import { FitAddon } from "xterm-addon-fit";
// xterm.js的附加组件，用于附加到Web Socket
import { AttachAddon } from "xterm-addon-attach";

export default {
  name: "HelloWorld",
  props: {
    socketURI: {
      type: String,
      default: "ws://127.0.0.1:8888",
    },
  },
  mounted() {
    this.initSocket();
  },
  beforeDestroy() {
    this.socket.close();
    this.term.dispose();
  },
  methods: {
    initTerm() {
      const term = new Terminal({
        fontSize: 20,
        cursorBlink: true,
      });
      const attachAddon = new AttachAddon(this.socket);
      const fitAddon = new FitAddon();
      term.loadAddon(attachAddon);
      term.loadAddon(fitAddon);
      term.open(document.getElementById("xterm"));
      fitAddon.fit();
      term.focus();
      this.term = term;
    },
    initSocket() {
      this.socket = new WebSocket(this.socketURI);
      this.socketOnClose();
      this.socketOnOpen();
      this.socketOnError();
    },
    socketOnOpen() {
      this.socket.onopen = () => {
        // 链接成功后
        this.initTerm();
      };
    },
    socketOnClose() {
      this.socket.onclose = function (event) {
        console.log("socket close:", event);
      };
    },
    socketOnError() {
      this.socket.onerror = function (event) {
        console.log("socket error:", event);
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
