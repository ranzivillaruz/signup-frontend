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
          <th>Signed Up At</th> </tr>
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
      // Use environment variable for API_BASE
      // Vercel/Vite automatically exposes VITE_ prefixed env vars to the browser
      API_BASE: import.meta.env.VITE_APP_API_BASE || 'http://localhost:3000'
    }
  },
  methods: {
    async submitForm() {
      try {
        const response = await fetch(`${this.API_BASE}/api/signups`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ name: this.name, email: this.email }),
        });

        if (!response.ok) {
          const errorData = await response.json();
          console.error('Submission failed:', errorData.error);
          // Optional: Display user-friendly error toast
        } else {
          const successData = await response.json();
          console.log('Submission successful:', successData.message);
          // Optional: Display user-friendly success toast
        }
      } catch (error) {
        console.error('Network or other error during submission:', error);
        // Optional: Display user-friendly network error toast
      }

      this.name = '';
      this.email = '';
      this.getSignups(); // Refresh the list after submission
    },
    async getSignups() {
      try {
        const res = await fetch(`${this.API_BASE}/api/signups`);
        if (!res.ok) {
          const errorData = await res.json();
          console.error('Failed to fetch signups:', errorData.error);
          this.signups = []; // Clear previous data on error
        } else {
          this.signups = await res.json();
        }
      } catch (error) {
        console.error('Network or other error during signup fetch:', error);
        this.signups = []; // Clear on network error
      }
    }
  },
  mounted() {
    this.getSignups(); // Fetch signups when the component mounts
  }
}
</script>

<style scoped>
/* (Your existing styles here) */
</style>