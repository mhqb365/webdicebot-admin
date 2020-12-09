<template>
  <div>
    <Nav />

    <div class="container p-4">
      <router-link to="/price/add">
        <button type="button" class="btn btn-primary mb-3">Add</button>
      </router-link>

      <div class="table-responsive-sm">
        <div v-if="isLoading" class="spinner-border"></div>

        <table v-else class="table table-bordered table-sm bg-white">
          <thead>
            <th>Limit</th>
            <th>Amount</th>
            <th>Action</th>
          </thead>
          <tbody>
            <tr v-for="price in datas" :key="price._id" :id="price._id">
              <td>{{ price.limit }} Days</td>
              <td>{{ price.amount.pay.toLocaleString() }} Doge</td>
              <td>
                <button
                  type="button"
                  class="btn btn-danger btn-sm"
                  @click="confirm(price._id)"
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
      perPage: 20,
      currentPage: 1,
    };
  },
  mounted: function () {
    this.fetch();
  },
  computed: {
    rows() {
      return this.datas.length;
    },
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
          `/price?limit=${this.perPage}&page=${page ? page : this.currentPage}`,
        method: "GET",
      })
        .then((response) => {
          this.isLoading = !this.isLoading;
          // console.log(response.data);
          this.datas = response.data.docs;
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
          // console.log(result);
          if (result.value)
            return axios({
              url: API_URL + "/price/" + id,
              method: "DELETE",
              headers: {
                token: localStorage.getItem("token"),
              },
            }).then((response) => {
              $("#" + id).remove();
              this.showAlert("Delete success");
            });
        });
    },
  },
};
</script>

<style>
</style>