<template>
  <v-navigation-drawer
    v-model="$store.state.drawer.userDrawerOpen"
    class="user-sidebar"
    app
    right
    :stateless="true"
    :width="250"
  >
    <v-expansion-panels
      accordion
      hover
      mandatory
    >
      <v-expansion-panel>
        <v-expansion-panel-header>
          <div
            class="text-truncate title"
          >
            {{ username }}
          </div>
        </v-expansion-panel-header>
        <v-expansion-panel-content
          class="user-panel-container"
        >
          <transition
            name="slide-x-reverse-transition"
            mode="out-in"
          >
            <user-menu
              v-if="isAuthenticated"
              key="authed"
              @logged-out="isLoggingIn = true"
            />
            <login
              v-else-if="isLoggingIn"
              key="login"
              @register="isRegistering = true; isLoggingIn = false"
              @logged-in="isLoggingIn = false"
            />
            <register
              v-else-if="isRegistering"
              key="register"
              @login="isRegistering = false; isLoggingIn = true"
              @registered="isRegistering = false"
            />
          </transition>
        </v-expansion-panel-content>
      </v-expansion-panel>

      <transition
        name="scroll-y-transition"
      >
        <v-expansion-panel
          v-if="isAuthenticated"
        >
          <v-expansion-panel-header>
            <div
              class="text-truncate title"
            >
              Quick List
            </div>
          </v-expansion-panel-header>
          <v-expansion-panel-content
            class="quick-list-container"
          >
            <quick-list />
          </v-expansion-panel-content>
        </v-expansion-panel>
      </transition>
    </v-expansion-panels>
  </v-navigation-drawer>
</template>

<script>
import QuickList from '~/components/QuickList'
import UserMenu from '~/components/User/UserMenu'
import Login from '~/components/Auth/Login'
import Register from '~/components/Auth/Register'

export default {
  name: 'UserSideBar',

  components: {
    QuickList,
    UserMenu,
    Login,
    Register,
  },

  props: [

  ],

  data: () => ({
    isLoggingIn: true,
    isRegistering: false,
  }),

  computed: {
    isAuthenticated () {
      return this.$store.state.auth.isAuthenticated
    },

    username () {
      return this.$store.state.auth.isAuthenticated
        ? this.$store.state.auth.user.username
        : 'Anonymous'
    },
  },

  created () {
    this.isLoggingIn = !this.isAuthenticated
  },

  methods: {

  }
}
</script>

<style>
  .user-sidebar > .v-navigation-drawer__content{
    padding-left:1px;
  }

  .quick-list-container > .v-expansion-panel-content__wrap{
    padding: 0px;
  }

  .user-panel-container > .v-expansion-panel-content__wrap{
    padding:0px;
  }
</style>
