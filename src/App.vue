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
          <th>Email</th></tr>
      </thead>
      <tbody>
        <tr v-for="entry in signups" :key="entry.id">
          <td>{{ entry.name }}</td>
          <td>{{ entry.email }}</td>
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
      // Ensure this matches where your backend is running
      // If backend is local: 'http://localhost:3000'
      // If backend is deployed: 'https://your-render-backend-url.onrender.com' (example)
      API_BASE: 'http://localhost:3000'
    }
  },
  methods: {
    async submitForm() {
      try {
        const response = await fetch(`${this.API_BASE}/api/signups`, { // FIXED: Changed '/signup' to '/api/signups'
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ name: this.name, email: this.email }),
        });

        if (!response.ok) {
          const errorData = await response.json();
          console.error('Submission failed:', errorData.error);
          // Optionally, show a user-friendly error message (e.g., using PrimeVue Toast)
          // this.$toast.add({severity:'error', summary: 'Error', detail: errorData.error, life: 3000});
        } else {
          const successData = await response.json();
          console.log('Submission successful:', successData.message);
          // Optionally, show a user-friendly success message (e.g., using PrimeVue Toast)
          // this.$toast.add({severity:'success', summary: 'Success', detail: successData.message, life: 3000});
        }
      } catch (error) {
        console.error('Network or other error during submission:', error);
        // this.$toast.add({severity:'error', summary: 'Error', detail: 'Failed to connect to server.', life: 3000});
      }

      this.name = '';
      this.email = '';
      this.getSignups(); // Refresh the list after submission
    },
    async getSignups() {
      try {
        const res = await fetch(`${this.API_BASE}/api/signups`); // FIXED: Changed '/signups' to '/api/signups'
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
/* You mentioned using Bootstrap classes like form-control, btn, btn-primary, table, table-bordered.
   Make sure Bootstrap CSS is correctly imported in your main.js or index.html if you want these styles.
   If using TailwindCSS, you would replace these with Tailwind classes.
   Example:
   .form-control { @apply block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none; }
   .btn { @apply inline-block px-6 py-2 border-2 border-gray-800 text-gray-800 font-medium text-xs leading-tight uppercase rounded hover:bg-black hover:bg-opacity-5 focus:outline-none focus:ring-0 transition duration-150 ease-in-out; }
   .btn-primary { @apply bg-blue-600 text-white hover:bg-blue-700; }
*/
</style>