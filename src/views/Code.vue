<template>
  <div>
    <Nav />

    <div class="container p-4">
      <router-link to="/code/add">
        <button type="button" class="btn btn-primary mb-2">Add</button>
      </router-link>

      <div class="mt-3 mb-3">
        Docs: {{ totalDocs }} | Pages: {{ totalPages }}
      </div>

      <ul class="pagination">
        <li class="page-item">
          <button class="page-link" v-if="hasPrevPage" @click="fetch(page - 1)">
            Previous
          </button>
        </li>
        <li class="page-item active">
          <a class="page-link" href="#">{{ page }}</a>
        </li>
        <li class="page-item">
          <button class="page-link" v-if="hasNextPage" @click="fetch(page + 1)">
            Next
          </button>
        </li>
      </ul>

      <div class="table-responsive-sm">
        <div v-if="isLoading" class="spinner-border"></div>

        <table v-else class="table table-bordered table-sm bg-white">
          <thead>
            <th>Name</th>
            <th>Type</th>
            <th>Content</th>
            <th>Author</th>
            <th>Action</th>
          </thead>
          <tbody>
            <tr v-for="data in datas" :key="data._id" :id="data._id">
              <td>{{ data.name }}</td>
              <td>{{ data.type }} - {{ data.typeUser }}</td>
              <td>{{ data.content.slice(0, 50) }}</td>
              <td>{{ data.author }}</td>
              <td>
                <button
                  type="button"
                  class="btn btn-danger btn-sm"
                  @click="confirm(data._id, 'delete')"
                >
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
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
      keyword: "",
      datas: [],
      perPage: 10,
      currentPage: 1,
      page: 1,
      totalDocs: 0,
      totalPages: 0,
      hasPrevPage: false,
      hasNextPage: false,
    };
  },
  mounted: function () {
    this.fetch();
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
    fetch: function (page) {
      this.isLoading = !this.isLoading;
      axios({
        url:
          API_URL +
          `/code/admin?limit=${this.perPage}&page=${
            page ? page : this.currentPage
          }`,
        method: "GET",
        headers: {
          token: localStorage.getItem("token"),
        },
      })
        .then((response) => {
          this.isLoading = !this.isLoading;
          this.datas = response.data.docs;
          this.page = response.data.page;
          this.totalDocs = response.data.totalDocs;
          this.totalPages = response.data.totalPages;
          this.hasPrevPage = response.data.hasPrevPage;
          this.hasNextPage = response.data.hasNextPage;
        })
        .catch(() => this.$router.push({ path: "/login" }));
    },
    confirm: function (id, action) {
      this.$swal
        .fire({
          title: "Are you sure?",
          showCancelButton: true,
        })
        .then((result) => {
          if (result.value) {
            switch (action) {
              case "delete":
                axios({
                  url: API_URL + "/code/delete/" + id,
                  method: "DELETE",
                  headers: {
                    token: localStorage.getItem("token"),
                  },
                }).then((response) => {
                  $("#" + id).remove();
                  this.showAlert("Delete success");
                });
                break;
            }
          }
        });
    },
  },
};
</script>

<style>
</style>