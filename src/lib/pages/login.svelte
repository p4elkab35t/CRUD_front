<script>  
    let loginData = {
      email: '',
      password: ''
    };
  
    const handleLogin = () => {
      console.log('Logging in:', loginData);
      // Add login logic here
    };
  </script>
  
  <main class="flex flex-col items-center justify-center min-h-screen bg-gray-50 p-4">
  
    <!-- Login Section -->
    <section class="w-full max-w-sm p-4 bg-white rounded-lg shadow-md">
      <h2 class="text-lg font-semibold text-gray-800 mb-4">Login</h2>
      <form on:submit|preventDefault={handleLogin} class="space-y-4">
        <div>
          <label for="login-email" class="block text-sm font-medium text-gray-700">Email</label>
          <input
            id="login-email"
            type="email"
            bind:value={loginData.email}
            class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            required
          />
        </div>
        <div>
          <label for="login-password" class="block text-sm font-medium text-gray-700">Password</label>
          <input
            id="login-password"
            type="password"
            bind:value={loginData.password}
            class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            required
          />
        </div>
        <button
          type="submit"
          class="w-full py-2 px-4 bg-blue-600 text-white font-medium rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500"
        >
          Login
        </button>
      </form>
    </section>
  </main>
  
  <style>
    main {
      font-family: 'Inter', sans-serif;
    }
  </style>
  