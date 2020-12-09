<template>
  <div>
    <Nav />

    <div class="container pt-4">
      <router-link to="/contact">
        <button type="button" class="btn btn-secondary mb-3">Cancel</button>
      </router-link>

      <div>
        <div v-if="isLoading" class="spinner-border"></div>

        <div v-else>
          <label>Name</label>
          <div class="form-group">
            <input type="text" class="form-control" v-model="name" />
          </div>

          <label>Phone number</label>
          <div class="form-group">
            <input type="text" class="form-control" v-model="phoneNumber" />
          </div>

          <button
            type="button"
            class="btn btn-primary btn-block"
            @click="add()"
          >
            Add
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Nav from "@/components/Nav";
import axios from "axios";
import API_URL from "@/utils/apiUrl";

export default {
  components: {
    Nav,
  },
  data() {
    return {
      isLoading: false,
      name: "",
      phoneNumber: "",
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
    add: function () {
      this.isLoading = !this.isLoading;
      axios({
        url: API_URL + "/contact",
        method: "POST",
        headers: {
          token: localStorage.getItem("token"),
        },
        data: {
          name: this.name,
          phoneNumber: this.phoneNumber,
        },
      }).then((response) => {
        this.isLoading = !this.isLoading;
        if (response.data.status == false)
          return this.showAlert(response.data.message, false);
        this.$router.push({ path: "/contact" });
      });
    },
  },
};
</script>

<style>
</style>