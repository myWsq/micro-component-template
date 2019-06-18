<template>
  <div class="main" :class="{ pt: navVisible }">
    <div class="components-nav" :class="{ show: navVisible }">
      <router-link
        class="nav"
        v-for="item in routes"
        :key="item.name"
        :to="item.path"
      >
        {{ item.name }}
      </router-link>
      <div class="trigger" :class="{ show: navVisible }" @click="onTrigger">
        <i class="el-icon-caret-bottom"></i>
      </div>
    </div>
    <div class="component-container">
      <div class="readme" v-show="isOnHome" v-html="readme"></div>
      <router-view></router-view>
    </div>
    <transition name="jelly">
      <div
        v-show="!isOnHome"
        class="fixed-btn"
        @click="$router.push('/')"
      ></div>
    </transition>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { routes } from "./router";
import Prism from "prismjs";
import "prismjs/components/prism-json";
import "prismjs/components/prism-bash";
import "./assets/prism-theme.css";
const COMPONENT_NAV_HIDDEN_FIELD = "componentNavHidden";
export default Vue.extend({
  data() {
    return {
      navVisible: true
    };
  },
  computed: {
    routes() {
      return routes;
    },
    isOnHome() {
      return this.$route.path === "/";
    },
    readme() {
      return require("!!html-loader!!!markdown-loader!../README.md");
    }
  },
  created() {
    const navHidden = localStorage.getItem(COMPONENT_NAV_HIDDEN_FIELD);
    if (navHidden) {
      this.navVisible = false;
    }
  },
  mounted() {
    Prism.highlightAll();
  },
  methods: {
    onTrigger() {
      this.navVisible = !this.navVisible;
      if (!this.navVisible) {
        localStorage.setItem(COMPONENT_NAV_HIDDEN_FIELD, "true");
      } else {
        localStorage.removeItem(COMPONENT_NAV_HIDDEN_FIELD);
      }
    }
  }
});
</script>

<style lang='scss' scoped>
.components-nav {
  box-shadow: 0 5px 10px -3px rgba(0, 0, 0, 0.1);
  height: 0;
  top: 0;
  width: 100%;
  background: #fff;
  position: fixed;
  transition: height 0.3s ease-in-out;
  overflow: hidden;
  &.show {
    height: 3em;
  }
  z-index: 9999;
}

.trigger {
  position: fixed;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
  text-align: center;
  padding: 0 1em 3px;
  border-radius: 50%;
  box-shadow: inherit;
  cursor: pointer;
  top: 0;
  transition: 0.3s ease-in-out;
  color: #909399;
  i {
    transition: inherit;
    transform: translateY(100%);
  }
  &.show {
    top: 3em;
    i {
      transform: rotate(180deg) translateY(0);
    }
  }
}

.nav {
  display: inline-block;
  text-decoration: none;
  color: #2c3e50;
  height: 3em;
  line-height: 3em;
  margin: 0 1em;
}

.router-link-active {
  font-weight: bold;
  color: #42b983;
}
.main {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  transition: padding 0.3s ease-in-out;
  &.pt {
    padding-top: 50px;
  }
}
.component-container {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
}

.readme {
  flex-grow: 1;
  margin: 0 auto;
  max-width: 600px;
  background: url("./assets/logo-light.svg") no-repeat center;
  & /deep/ {
    color: #303133;
    line-height: 1.5;
    h1 {
      color: #42b983;
    }
    blockquote {
      color: #909399;
      background: linear-gradient(#409eff, #409eff) no-repeat;
      background-size: 3px 100%;
      margin: 0;
      padding-left: 2em;
    }
    h2 {
      font-weight: 400;
    }
  }
}

.fixed-btn {
  background: url("./assets/logo.svg") no-repeat;
  background-size: contain;
  height: 40px;
  width: 40px;
  position: fixed;
  bottom: 20px;
  right: 20px;
  cursor: pointer;
  transition: 0.3s;
  &:hover {
    transform: scale(1.2);
  }
  &:active {
    transform: scale(0.8);
  }
}

@keyframes jelly {
  0% {
    transform: scale(0);
  }

  30% {
    transform: scale(1);
  }

  60% {
    transform: scale(1.2);
  }

  100% {
    transform: scale(1);
  }
}

.jelly-enter-active {
  animation: jelly 0.3s ease-in;
}

.jelly-leave-active {
  animation: jelly 0.3s ease-in reverse;
}
</style>
<style>
body {
  margin: 0;
}
</style>
