<template>
  <div id="wrapper">
    <img id="logo" src="~@/assets/logo.png" alt="electroneum-logo">
    <main>
      <div class="left-side">
        <span class="title">
          Welcome to ElectroVault!
        </span>
        <system-information></system-information>
      </div>

      <div class="right-side">
        <div class="doc">
          <div class="title">Installation</div>
          <p>
            To get started with ElectroVault, just click install!
          </p>
          <router-link class="button primary" to="/install" style="font-weight: 600;">Install</router-link><br><br>
        </div>
        <div class="doc">
          <div class="title alt">Donate</div>
          <button class="button button-alt" @click="notif('BTC: ')">Bitcoin</button>
          <button class="button button-alt" @click="notif('ETN: ')">Electroneum</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
  import SystemInformation from './LandingPage/SystemInformation';

  var store = require('store');
  var fs = require('fs');
  const {
    ipcRenderer
  } = require('electron');
  const remote = require('electron').remote


  export default {
    name: 'landing-page',
    components: { SystemInformation },
    data() {
      return {
        w: remote.getCurrentWindow()
      }
    },
    methods: {
      notif (text) {
        alert(text)
      },

      close() {
        this.w.close();
      },

      open(link) {
        this.$electron.shell.openExternal(link);
      }
    },
    mounted: function() {
      var self = this;
      if (store.get('setupComplete') == true) {

        var electroDir = require('os').homedir() + '\\Desktop\\electrovault_wallet';

        if (!fs.existsSync(electroDir)) {
          store.set('setupComplete', false);
          self.close();
        } else if (fs.existsSync(electroDir)) {
          // Get path
          var executablePath = electroDir + '\\electroneumd.exe';

          // Send message to start Daemon
          console.log(ipcRenderer.sendSync('synchronous-message', {
            message: "start_daemon",
            path: executablePath
          }))

          // Move to wallet page
          self.$router.push('/wallet')
        }
      }
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  #wrapper {
    background:
      radial-gradient(
        ellipse at top left,
        rgba(255, 255, 255, 1) 40%,
        rgba(229, 229, 229, .9) 100%
      );
    height: 100vh;
    padding: 60px 80px;
    width: 100vw;
  }

  #logo {
    height: auto;
    margin-bottom: 20px;
    width: 100px;
  }

  main {
    display: flex;
    justify-content: space-between;
  }

  main > div { flex-basis: 50%; }

  .left-side {
    display: flex;
    flex-direction: column;
  }

  .welcome {
    color: #555;
    font-size: 23px;
    margin-bottom: 10px;
  }

  .title {
    color: #2c3e50;
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 6px;
  }

  .title.alt {
    font-size: 18px;
    margin-bottom: 10px;
  }

  .doc p {
    color: black;
    margin-bottom: 10px;
  }

  .doc .button {
    font-size: .9em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 2em;
    border-radius: 2em;
    display: inline-block;
    color: #fff;
    background-color: #2F77F7;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #2F77F7;
    margin-top: 10px;
    text-decoration: none;
    -webkit-app-region: no-drag;
  }

  .doc .button:hover {
    background-color: #2262d6;
  }

  .doc .button-alt {
    color: #3e3e3e;
    margin-right: 5px;
    background-color: transparent;
  }

  .doc .button-alt:hover {
    background-color: #e2e2e2;
  }

  .doc .button-info {
    border-color: #3e3e3e;
    color: #3e3e3e;
    margin-right: 5px;
    background-color: transparent;
  }

  .doc .button-info:hover {
    background-color: #e2e2e2;
  }
</style>
