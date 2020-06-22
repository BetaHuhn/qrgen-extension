<template>
  <div class="popup-wrapper">
    <div v-if="status === 'ready'">
      <vue-qrcode class="qr" v-bind:value="url" v-bind:color="{ dark: '#fff', light: '#0000' }" v-bind:width="250" v-bind:scale="1" v-bind:margin="0" />
      <div class="code">
        <h2>Your short link: <span @click="visitUrl" class="link">qrgen.cc/{{ code }}</span></h2>
      </div>
      <div class="options">
        <p v-if="!copy" class="copy link" @click="copyCode">Copy link</p>
        <p v-else class="copy">Copied!</p>
        <p class="or">or</p>
        <p @click="newShort" class="copy link">Create another one</p>
      </div>
    </div>
    <p v-else-if="status === 'error'" class="error">{{ errorMsg }}</p>
    <p v-else>Loading...</p>
  </div>
</template>

<script>
import VueQrcode from "vue-qrcode";

export default {
  data() {
    return {
      url: "",
      code: "",
      copy: false,
      status: "loading",
      errorMsg: "error"
    };
  },
  components: {
    VueQrcode
  },
  methods: {
    getUrl: function() {
      chrome.tabs.query(
        {
          currentWindow: true,
          active: true
        },
        this.create.bind(this)
      );
    },
    create: async function(tabs) {
      this.url = tabs[0].url;
      if (!this.validURL(this.url)) {
        this.status = "error";
        this.errorMsg = "Not a valid URL";
        return;
      }
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          url: this.url
        })
      };
      const response = await fetch("https://qrgen.cc/api/create", options);
      if (response.status === 400) {
        this.status = "error";
        return;
      }
      const json = await response.json();
      if (json.status === 200) {
        this.code = json.result.code;
        this.status = "ready";
      } else {
        this.status = "error";
        this.errorMsg = `An error ocurred`;
      }
    },
    validURL: function(value) {
      const expression = /[-a-zA-Z0-9@:%_\+.~#?&//=]{2,256}\.[a-z]{2,4}\b(\/[-a-zA-Z0-9@:%_\+.~#?&//=]*)?/gi;
      const regexp = new RegExp(expression);
      return regexp.test(value);
    },
    copyCode: function() {
      const value = "https://qrgen.cc/" + this.code;
      const body = document.getElementsByTagName("body")[0];
      const tempInput = document.createElement("INPUT");
      body.appendChild(tempInput);
      tempInput.setAttribute("value", value);
      tempInput.select();
      document.execCommand("copy");
      body.removeChild(tempInput);
      this.copy = true;
      setTimeout( () => {
        this.copy = false;
      }, 1000);
    },
    newShort: function() {
      chrome.tabs.create({
        url: "https://qrgen.cc/"
      });
    },
    visitUrl: function() {
      chrome.tabs.create({
        url: "https://qrgen.cc/" + this.code
      });
    }
  },
  created: function() {
    this.getUrl();
  }
};
</script>

<style>
:root {
  --primary: #60ddaf;
  --font: #fff;
  --bg: #282d2d;
  --error: #dd6060;
}

html,
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  color: var(--font);
  background-color: var(--bg);
  font-size: 15px;
  width: 400px;
  overflow-x: hidden;
}

.popup-wrapper {
  width: 100%;
  text-align: center;
  padding: 10px 10px;
}

.link {
  text-decoration: none;
  color: var(--primary);
  cursor: pointer;
}

.link:hover {
  opacity: 0.9;
}

.error {
  color: var(--error);
}

.options {
  text-align: center;
}

.copy {
  font-weight: 700;
  font-style: normal;
  text-align: center;
  display: inline-block;
}

.or {
  font-weight: 700;
  font-style: normal;
  color: var(--font);
  text-align: center;
  display: inline-block;
  margin: 5px;
}

.qr {
  margin-top: 2rem;
}
</style>
