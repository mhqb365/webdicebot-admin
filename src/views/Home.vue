<template>
  <div>
    <Nav />

    <div class="container pt-4">
      <div class="btn-group mb-3">
        <button
          type="button"
          class="btn"
          v-bind:class="[isActive == 'total' ? 'btn-primary' : 'btn-light']"
          @click="filter('total')"
        >
          Total
        </button>
        <button
          type="button"
          class="btn"
          v-bind:class="[isActive == 'last' ? 'btn-primary' : 'btn-light']"
          @click="filter('last')"
        >
          Last month
        </button>
        <button
          type="button"
          class="btn"
          v-bind:class="[isActive == '' ? 'btn-primary' : 'btn-light']"
          @click="filter()"
        >
          This month
        </button>
      </div>

      <div class="row text-white text-center mb-3">
        <div class="col-sm-12 col-md-3">
          <div class="rounded p-4 bg-warning mb-2">
            Income DOGE
            <h4>
              <span v-if="isLoading">?</span>
              <span v-else>{{ doge.toLocaleString() }}</span>
            </h4>
          </div>
        </div>

        <div class="col-sm-12 col-md-3">
          <div class="rounded p-4 bg-primary mb-2">
            License
            <h4>
              <span v-if="isLoading">?</span>
              <span v-else>{{ license }}</span>
            </h4>
          </div>
        </div>

        <div class="col-sm-12 col-md-3">
          <div class="rounded p-4 bg-success mb-2">
            Pay
            <h4>
              <span v-if="isLoading">?</span>
              <span v-else>{{ pay }}</span>
            </h4>
          </div>
        </div>

        <div class="col-sm-12 col-md-3">
          <div class="rounded p-4 bg-danger mb-2">
            Free
            <h4>
              <span v-if="isLoading">?</span>
              <span v-else>{{ free }}</span>
            </h4>
          </div>
        </div>
      </div>

      <div class="row text-center mb-3">
        <div class="col-sm-12 col-md-3">
          <router-link to="/price">
            <div class="card mb-2">
              <div class="card-body">Price</div>
            </div>
          </router-link>
        </div>

        <div class="col-sm-12 col-md-3">
          <router-link to="/license">
            <div class="card mb-2">
              <div class="card-body">License</div>
            </div>
          </router-link>
        </div>

        <div class="col-sm-12 col-md-3">
          <router-link to="/ref">
            <div class="card mb-2">
              <div class="card-body">Ref</div>
            </div>
          </router-link>
        </div>

        <div class="col-sm-12 col-md-3">
          <router-link to="/contact">
            <div class="card mb-2">
              <div class="card-body">Contact</div>
            </div>
          </router-link>
        </div>

        <div class="col-sm-12 col-md-3">
          <router-link to="/login">
            <div class="card mb-2" @click="logout()">
              <div class="card-body">Logout</div>
            </div>
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Nav from "@/components/Nav";
import axios from "axios";
import API_URL from "@/utils/apiUrl";
import moment from "moment";

export default {
  components: {
    Nav,
  },
  data() {
    return {
      isLoading: false,
      isActive: "",
      licenses: [],
      license: 0,
      free: 0,
      pay: 0,
      doge: 0,
      from: 0,
      to: 0,
    };
  },
  mounted: function () {
    this.fetch();
  },
  methods: {
    fetch: function () {
      this.isLoading = !this.isLoading;
      axios({
        url: API_URL + "/license",
        method: "GET",
        headers: {
          token: localStorage.getItem("token"),
        },
      })
        .then((response) => {
          this.isLoading = !this.isLoading;
          this.licenses = response.data;
          this.filter();
        })
        .catch(() => this.$router.push({ path: "/login" }));
    },
    filter: function (type) {
      switch (type) {
        case "total":
          this.isActive = "total";
          this.from = moment("20200329");
          this.to = moment().endOf("month");
          break;
        case "last":
          this.isActive = "last";
          this.from = moment().subtract(1, "months").startOf("month");
          this.to = moment().subtract(1, "months").endOf("month");
          break;
        default:
          this.isActive = "";
          this.from = moment().startOf("month");
          this.to = moment().endOf("month");
          break;
      }

      this.license = 0;
      this.doge = 0;
      this.free = 0;
      this.pay = 0;
      this.licenses.map((license) => {
        if (
          new Date(license.time) >= new Date(this.from) &&
          new Date(license.time) <= new Date(this.to)
        ) {
          this.license += 1;
          if (license.type == "free") {
            this.free++;
          } else {
            this.pay++;
            this.doge += license.price.amount.pay;
          }
        }
      });
    },
    logout: function () {
      localStorage.removeItem("token");
      this.$router.push({ path: "/login" });
    },
  },
};
</script>

<style>
</style>