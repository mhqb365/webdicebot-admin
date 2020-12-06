<template>
  <nav class="navbar navbar-expand-md bg-dark navbar-dark">
    <!-- Brand -->

    <router-link
      to="/"
      class="text-white"
      v-bind:class="[this.$route.path == '/' ? 'active' : '']"
    >
      <img
        src="/static/logo.png"
        class="rounded-circle mr-2 border border-white"
        width="42px"
      />
    </router-link>

    <!-- Toggler/collapsibe Button -->
    <button
      class="navbar-toggler"
      type="button"
      data-toggle="collapse"
      data-target="#collapsibleNavbar"
    >
      <span class="navbar-toggler-icon"></span>
    </button>

    <!-- Navbar links -->
    <div class="collapse navbar-collapse" id="collapsibleNavbar">
      <ul class="navbar-nav">
        <li class="nav-item">
          <router-link
            to="/price"
            class="nav-link"
            v-bind:class="[
              this.$route.path == '/price' || this.$route.path == '/price/add'
                ? 'active'
                : '',
            ]"
          >
            Price
          </router-link>
        </li>

        <li class="nav-item">
          <router-link
            to="/license"
            class="nav-link"
            v-bind:class="[
              this.$route.path == '/license' ||
              this.$route.path == '/license/add'
                ? 'active'
                : '',
            ]"
          >
            License
          </router-link>
        </li>

        <li class="nav-item">
          <router-link
            to="/ref"
            class="nav-link"
            v-bind:class="[
              this.$route.path == '/ref' || this.$route.path == '/ref/add'
                ? 'active'
                : '',
            ]"
          >
            Ref
          </router-link>
        </li>

        <li class="nav-item">
          <router-link
            to="/contact"
            class="nav-link"
            v-bind:class="[
              this.$route.path == '/contact' ||
              this.$route.path == '/contact/add'
                ? 'active'
                : '',
            ]"
          >
            Contact
          </router-link>
        </li>

        <li class="nav-item">
          <a href="#" class="nav-link" @click="logout()">Logout</a>
        </li>
      </ul>
    </div>
  </nav>
</template>

<script>
export default {
  methods: {
    logout: function () {
      localStorage.removeItem("token");
      this.$router.push({ path: "/login" });
    },
  },
};
</script>

<style>
</style>