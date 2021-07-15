<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="bg-white text-grey-10" bordered>
      <q-toolbar class="constrain">
        <q-btn
          class="large-screen-only"
          to="/camera"
          flat
          round
          dense
          size="1rem"
          icon="eva-video-outline"
        />

        <q-separator class="large-screen-only" vertical spaced inset />

        <q-toolbar-title class="text-grand-hotel text-bold">
          Bagram
        </q-toolbar-title>

        <q-btn
          class="large-screen-only"
          to="/"
          flat
          round
          dense
          size="1rem"
          icon="eva-home-outline"
        />
      </q-toolbar>
    </q-header>
    <q-footer bordered class="bg-white">
      <transition
        appear
        enter-active-class="animated bounceInLeft"
        leave-active-class="animated bounceOutRight"
      >
        <div v-if="showAppInstallBanner" class="banner-container bg-primary">
          <div class="constrain">
            <q-banner inline-actions dense class="bg-primary text-white">
              <template v-slot:avatar>
                <q-avatar
                  color="white"
                  text-color="grey-10"
                  font-size=".9em "
                  icon="eva-home-outline"
                />
              </template>

              Install Bagram?

              <template v-slot:action>
                <q-btn
                  @click="installApp"
                  flat
                  class="q-px-sm"
                  label="Yes!"
                  dense
                />
                <q-btn
                  @click="showAppInstallBanner = false"
                  flat
                  class="q-px-sm"
                  label="Later"
                  dense
                />
                <q-btn
                  @click="neverShowAppInstallBanner"
                  flat
                  class="q-px-sm"
                  label="Never"
                  dense
                />
              </template>
            </q-banner>
          </div>
        </div>
      </transition>

      <q-tabs
        active-color="primary"
        class="text-grey-10 small-screen-only"
        indicator-color="transparent"
      >
        <q-route-tab to="/" icon="eva-home-outline" />
        <q-route-tab to="/camera" icon="eva-video-outline" />
      </q-tabs>
    </q-footer>
    <q-page-container class="bg-grey-1">
      <keep-alive :include="['PageHome']">
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
let deferredPrompt;
export default {
  name: "MainLayout",

  data() {
    return {
      showAppInstallBanner: false
      // neverShowAppInstallBanner: false
    };
  },
  methods: {
    installApp() {
      this.showAppInstallBanner = false;
      deferredPrompt.prompt();
      deferredPrompt.userChoice.then(choiceResult => {
        if (choiceResult.outcome === "accepted") {
          console.log("Accepted to install");
          this.neverShowAppInstallBanner();
        } else {
          console.log("Dismissed the install");
        }
      });
    },
    neverShowAppInstallBanner() {
      this.showAppInstallBanner = false;
      this.$q.localStorage.set("neverShowAppInstallBanner", true);
    }
  },
  mounted() {
    let neverShowAppInstallBanner = this.$q.localStorage.getItem(
      "neverShowAppInstallBanner"
    );

    if (!neverShowAppInstallBanner) {
      window.addEventListener("beforeinstallprompt", e => {
        e.preventDefault();
        deferredPrompt = e;
        setTimeout(() => {
          this.showAppInstallBanner = true;
        }, 3000);
      });
    }
  }
};
</script>
<style lang="scss">
.q-toolbar {
  @media (min-width: $breakpoint-sm-min) {
    height: 77px;
  }
}
.q-toolbar__title {
  font-size: 2em;
  @media (max-width: $breakpoint-xs-max) {
    text-align: center;
  }
}
.q-footer {
  .q-tab__icon {
    font-size: 2em;
  }
}
</style>
