<template>
  <div class="container p-4">
    <div v-if="isLoading" class="spinner-border"></div>

    <div v-else class="row justify-content-center">
      <div class="col-sm-12 col-md-6">
        <label>User name</label>
        <div class="form-group">
          <input type="text" class="form-control" v-model="userName" />
        </div>

        <label>Password</label>
        <div class="form-group">
          <input type="password" class="form-control" v-model="password" />
        </div>

        <button
          type="button"
          class="btn btn-primary btn-block"
          @click="login()"
        >
          Login
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import API_URL from "@/utils/apiUrl";

export default {
  data() {
    return {
      isLoading: false,
      userName: "",
      password: "",
    };
  },
  methods: {
    showAlert: function (message, type = true) {
      this.$swal.fire({
        icon: `${type ? "success" : "error"}`,
        title: message,
        showConfirmButton: false,
        timer: 2e3,
      });
    },
    login: function () {
      this.isLoading = !this.isLoading;
      axios({
        url: API_URL + "/user/login",
        method: "POST",
        data: {
          userName: this.userName,
          password: this.password,
        },
      }).then((response) => {
        this.isLoading = !this.isLoading;
        if (!response.data.status)
          return this.showAlert(response.data.message, false);
        localStorage.setItem("token", response.data.token);
        this.$router.push({ path: "/" });
      });
    },
  },
};
</script>

<style>
</style>