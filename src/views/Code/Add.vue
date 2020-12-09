<template>
  <div>
    <Nav />

    <div class="container p-4">
      <router-link to="/code">
        <button type="button" class="btn btn-secondary mb-3">Cancel</button>
      </router-link>

      <div>
        <div v-if="isLoading" class="spinner-border"></div>

        <div v-else>
          <div class="form-group">
            <label>Language</label>
            <select class="form-control" v-model="type">
              <option>Lua</option>
              <option>Javascript</option>
            </select>
          </div>

          <div class="form-group">
            <label>Type</label>
            <select class="form-control" v-model="typeUser">
              <option>Normal</option>
              <option>Vip</option>
            </select>
          </div>

          <div class="form-group">
            <label>Code name</label>
            <input type="text" class="form-control" v-model="name" />
          </div>

          <div class="form-group">
            <label>Share by</label>
            <input type="text" class="form-control" v-model="author" />
          </div>

          <div class="form-group">
            <label>Content</label>
            <textarea class="form-control" rows="5" id="content"></textarea>
          </div>

          <button type="button" class="btn btn-primary btn-block" @click="add()">
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
      type: "Lua",
      typeUser: "Normal",
      content: "",
      author: "",
      simplemde: "",
    };
  },
  mounted: function () {
    this.simplemde = new SimpleMDE({
      element: document.getElementById("content"),
    });
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
        url: API_URL + "/code",
        method: "POST",
        headers: {
          token: localStorage.getItem("token"),
        },
        data: {
          name: this.name,
          type: this.type,
          typeUser: this.typeUser,
          content: this.simplemde.value(),
          author: this.author,
        },
      }).then((response) => {
        this.isLoading = !this.isLoading;
        if (response.data.status == false)
          return this.showAlert(response.data.message, false);
        this.$router.push({ path: "/code" });
      });
    },
  },
};
</script>

<style>
</style>