<template>
  <div>
    <Nav />

    <div class="container p-4">
      <router-link to="/ref/add">
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
            <th>Title</th>
            <th>Url</th>
            <th>Action</th>
          </thead>
          <tbody>
            <tr v-for="data in datas" :key="data._id">
              <td>{{ data.title.slice(0, 32) }}</td>
              <td>{{ data.url }}</td>
              <td>
                <button
                  type="button"
                  class="btn btn-danger btn-sm"
                  @click="confirm(data._id)"
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
      datas: [],
      perPage: 10,
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
    fetch: function (page) {
      this.isLoading = !this.isLoading;
      axios({
        url:
          API_URL +
          `/ref?limit=${this.perPage}&page=${page ? page : this.page}`,
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
    confirm: function (id) {
      this.$swal
        .fire({
          title: "Are you sure?",
          showCancelButton: true,
        })
        .then((result) => {
          if (result.value)
            return axios({
              url: API_URL + "/ref/delete/" + id,
              method: "DELETE",
              headers: {
                token: localStorage.getItem("token"),
              },
            }).then((response) => {
              this.fetch();
            });
        });
    },
  },
};
</script>

<style>
</style>