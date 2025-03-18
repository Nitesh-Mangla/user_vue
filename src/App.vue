<template>
  <div class="d-flex flex-row lp-2">
    <input type="text" placeholder="Search by name" name="search_by_name" v-model="token"
           @input="setSearch">
    <button class="btn btn-info" @click="fetchUserDetails(1, limit)">Search</button>
  </div>
  <table class="table table-striped table-bordered table-responsive-lg">
    <thead>
      <tr>
        <th scope="col">S.No</th>
        <th scope="col">First Name</th>
        <th scope="col">Last Name</th>
        <th scope="col">Email</th>
        <th scope="col">Organization</th>
        <th scope="col">Created On</th>
      </tr>
    </thead>
    <tbody>
    <tr v-for="user in users" :key="user.id">
      <td>{{ user.id }}</td>
      <td>{{ user.first_name }}</td>
      <td>{{ user.last_name }}</td>
      <td>{{ user.email }}</td>
      <td>{{ user.organization }}</td>
      <td>{{ formatDate(user.created_on) }}</td>
    </tr>
    </tbody>
  </table>
  <div class="pagination d-flex justify-content-center">
    <button class="btn btn-primary" :disabled="page === 1" @click="fetchUserDetails(page-1)">Prev</button>
    <button class="btn btn-primary" :disabled="parseInt(limit) * parseInt(page)+1 >= total" @click="fetchUserDetails(page+1, limit)">Next</button>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      users: [],
      page: 1,
      limit: 15,
      total: 0,
      search_by_name: ''
    };
  },
  methods: {
    setSearch()
    {
      if (this.token) {
        this.search_by_name = this.token;
      }
    },
    async fetchUserDetails(page = 1, limit = 15) {
      try {
        this.limit = limit;
        const response = await axios.get(`http://localhost:3000/users?page=${page}&limit=${this.limit}&first_name=${this.search_by_name}`, {
          headers: {
            authorization:'J23HRGVB2EREBWN',
          }
        });
        const res = response.data
        this.users = res.data;
        this.total = res.total;
        this.page = res.page;
        this.limit = res.limit;

      } catch (error) {
        console.error('Error fetching user details:', error);
      }
    },
    formatDate(date) {
      return new Date(date).toLocaleString();
    },
  },
  mounted() {
    this.fetchUserDetails();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>


