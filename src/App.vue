<!-- frontend/src/App.vue -->
<template>
  <div class="container py-5">
    <h2 class="mb-4 text-center">Signup Form</h2>
    <form @submit.prevent="submitForm" class="mb-5">
      <div class="mb-3">
        <label>Name</label>
        <input v-model="name" class="form-control" required />
      </div>
      <div class="mb-3">
        <label>Email</label>
        <input v-model="email" class="form-control" required />
      </div>
      <button class="btn btn-primary">Submit</button>
    </form>

 <h3>All Signups</h3>
<table class="table table-bordered">
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th>Time</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="entry in signups" :key="entry.id">
      <td>{{ entry.name }}</td>
      <td>{{ entry.email }}</td>
      <td>
        {{ entry.createdAt && entry.createdAt.seconds ? new Date(entry.createdAt.seconds * 1000).toLocaleString() : 'N/A' }}
      </td>
    </tr>
  </tbody>
</table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: '',
      email: '',
      signups: [],
      API_BASE: 'http://localhost:3000' // change this to Render URL when deployed
    }
  },
  methods: {
    async submitForm() {
      await fetch(`${this.API_BASE}/signup`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: this.name, email: this.email }),
      });
      this.name = '';
      this.email = '';
      this.getSignups();
    },
    async getSignups() {
      const res = await fetch(`${this.API_BASE}/signups`);
      this.signups = await res.json();
    }
  },
  mounted() {
    this.getSignups();
  }
}
</script>
