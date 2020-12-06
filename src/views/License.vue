<template>
  <div>
    <Nav />

    <div class="container pt-4">
      <router-link to="/license/add">
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

      <div class="input-group mb-3">
        <input
          type="text"
          class="form-control"
          placeholder="Search by email"
          v-model="keyword"
          @change="search()"
        />
        <div class="input-group-append">
          <button
            type="button"
            class="btn btn-secondary"
            @click="emptyKeyWord()"
          >
            Clear
          </button>
        </div>
      </div>

      <div class="table-responsive-sm">
        <div v-if="isLoading" class="spinner-border"></div>

        <table v-else class="table table-bordered table-sm bg-white">
          <thead>
            <th>Time</th>
            <th>Email</th>
            <th>Limit</th>
            <th>Value</th>
            <th>Action</th>
          </thead>
          <tbody>
            <tr v-for="data in datas" :key="data._id">
              <td>{{ new Date(data.time).toLocaleDateString("vi-VN") }}</td>
              <td>
                <span
                  v-if="
                    data.locked ||
                    (Date.now() - new Date(data.time)) / 864e5 >
                      data.price.limit
                  "
                >
                  <strike
                    v-bind:class="{ 'text-primary': data.type == 'pay' }"
                    >{{ data.email }}</strike
                  >
                </span>
                <span
                  v-else
                  v-bind:class="{ 'text-primary': data.type == 'pay' }"
                  >{{ data.email }}</span
                >
              </td>
              <td>{{ data.price.limit }}</td>
              <td>
                <div class="input-group mb-2 input-group-sm">
                  <input
                    type="password"
                    class="form-control"
                    v-model="data.value"
                  />
                  <div class="input-group-append">
                    <button
                      type="button"
                      class="btn btn-primary"
                      v-clipboard="() => data.value"
                      v-clipboard:success="clipboardSuccess"
                      v-clipboard:error="clipboardError"
                    >
                      Copy
                    </button>
                  </div>
                </div>
              </td>
              <td>
                <button
                  v-if="
                    (Date.now() - new Date(data.time)) / 864e5 <
                    data.price.limit
                  "
                  type="button"
                  class="btn btn-warning btn-sm mb-2"
                  @click="
                    confirm(data._id, `${data.locked ? 'unlock' : 'lock'}`)
                  "
                >
                  {{ data.locked ? "Unlock" : "Lock" }}
                </button>
                <button
                  type="button"
                  class="btn btn-danger btn-sm mb-2"
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
          `/license?limit=${this.perPage}&page=${page ? page : this.page}`,
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
    search: function (page) {
      if (this.keyword == "") return this.fetch();
      this.isLoading = !this.isLoading;
      axios({
        url:
          API_URL +
          `/license/search/${this.keyword}?limit=${this.perPage}&page=${
            page ? page : this.page
          }`,
        method: "GET",
        headers: {
          token: localStorage.getItem("token"),
        },
      }).then((response) => {
        this.isLoading = !this.isLoading;
        this.datas = response.data.docs;
      });
    },
    confirm: function (id, action) {
      this.$swal
        .fire({
          title: "Are you sure?",
          showCancelButton: true,
        })
        .then((result) => {
          if (result.value) return this.action(id, action);
        });
    },
    action: function (id, action) {
      if (action == "delete")
        return axios({
          url: API_URL + "/license/" + id,
          method: "DELETE",
          headers: {
            token: localStorage.getItem("token"),
          },
        }).then((response) => {
          this.fetch();
        });

      axios({
        url: API_URL + "/license/" + action + "/" + id,
        method: "PUT",
        headers: {
          token: localStorage.getItem("token"),
        },
      }).then((response) => {
        this.fetch();
      });
    },
    emptyKeyWord: function () {
      this.keyword = "";
      this.fetch();
    },
  },
};
</script>

<style>
</style>