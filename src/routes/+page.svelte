<script lang="ts">
    import { onMount } from 'svelte';
  
    interface RegisterData {
      email: string;
      password: string;
    }
  
    interface LoginData {
      email: string;
      password: string;
    }
  
    interface Todo {
      id: number;
      task: string;
    }
  
    let registerData: RegisterData = { email: '', password: '' };
    let loginData: LoginData = { email: '', password: '' };
    let todos: Todo[] = [];
    let newTodo: string = '';
    let isAuthenticated: boolean = false;
    let isRegsitration: boolean = false;
  
    const API_URL = 'http://localhost:3000';
  
    async function handleRegister(event: Event): Promise<void> {
      event.preventDefault();
      try {
        const res = await fetch(`${API_URL}/register`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(registerData),
        });
        if (res.ok) {
          alert('Registration successful. Please login.');
        } else {
          alert('Registration failed.');
        }
      } catch (error) {
        console.error('Error registering:', error);
      }
    }
  
    async function handleLogin(event: Event): Promise<void> {
      event.preventDefault();
      try {
        const res = await fetch(`${API_URL}/login`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(loginData),
        });
        if (res.ok) {
          const data = await res.json();
          localStorage.setItem('token', data.token);
          isAuthenticated = true;
          fetchTodos();
        } else {
          alert('Login failed.');
        }
      } catch (error) {
        console.error('Error logging in:', error);
      }
    }
  
    async function fetchTodos(): Promise<void> {
      try {
        const token = localStorage.getItem('token');
        const res = await fetch(`${API_URL}/list`, {
          headers: { Authorization: `Bearer ${token}` },
        });
        if (res.ok) {
          todos = await res.json();
        } else {
          alert('Failed to fetch todos.');
        }
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    }
  
    async function addTodo(): Promise<void> {
      try {
        const token = localStorage.getItem('token');
        const res = await fetch(`${API_URL}/list`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${token}`,
          },
          body: JSON.stringify({ task: newTodo }),
        });
        if (res.ok) {
          newTodo = '';
          fetchTodos();
        } else {
          alert('Failed to add todo.');
        }
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    }
  
    async function deleteTodo(id: number): Promise<void> {
      try {
        const token = localStorage.getItem('token');
        const res = await fetch(`${API_URL}/list/${id}`, {
          method: 'DELETE',
          headers: { Authorization: `Bearer ${token}` },
        });
        if (res.ok) {
          fetchTodos();
        } else {
          alert('Failed to delete todo.');
        }
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    }
  
    async function logout(): Promise<void> {
      try {
        const token = localStorage.getItem('token');
        await fetch(`${API_URL}/deauth`, {
          headers: { Authorization: `Bearer ${token}` },
        });
        localStorage.removeItem('token');
        isAuthenticated = false;
        todos = [];
      } catch (error) {
        console.error('Error logging out:', error);
      }
    }
  
    // Check authentication on load
    onMount(async () => {
      try {
        const token = localStorage.getItem('token');
        if (token) {
          const res = await fetch(`${API_URL}/auth`, {
            headers: { Authorization: `Bearer ${token}` },
          });
          isAuthenticated = res.ok;
          if (isAuthenticated) fetchTodos();
        }
      } catch (error) {
        console.error('Error during authentication check:', error);
      }
    });
  </script>
  
  <main class="flex flex-col items-center justify-center min-h-screen bg-gray-50 p-4">
    {#if !isAuthenticated}
    {#if isRegsitration}
      <!-- Register Section -->
      <section class="w-full max-w-sm p-4 mb-6 bg-white rounded-lg shadow-md">
        <h2 class="text-lg font-semibold text-gray-800 mb-4">Register</h2>
        <form on:submit={handleRegister} class="space-y-4">
          <div>
            <label for="register-email" class="block text-sm font-medium text-gray-700">Email</label>
            <input
              id="register-email"
              type="email"
              bind:value={registerData.email}
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              required
            />
          </div>
          <div>
            <label for="register-password" class="block text-sm font-medium text-gray-700">Password</label>
            <input
              id="register-password"
              type="password"
              bind:value={registerData.password}
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              required
            />
          </div>
          <button
            type="submit"
            class="w-full py-2 px-4 bg-blue-600 text-white font-medium rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            Register
          </button>
          <button
            on:click={(e) => {e.preventDefault(); isRegsitration = false}}
            class="w-full py-2 px-4 bg-gray-600 text-white font-medium rounded-md hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-gray-500"
            >
            Login instead
            </button>
        </form>
      </section>
    {:else}
      <!-- Login Section -->
      <section class="w-full max-w-sm p-4 mb-6 bg-white rounded-lg shadow-md">
        <h2 class="text-lg font-semibold text-gray-800 mb-4">Login</h2>
        <form on:submit={handleLogin} class="space-y-4">
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
          <button
            on:click={(e) => {e.preventDefault(); isRegsitration = true}}
            class="w-full py-2 px-4 bg-gray-600 text-white font-medium rounded-md hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-gray-500"
            >
            Register instead
            </button>
        </form>
      </section>
    {/if}
    {:else}
      <!-- Todo List Section -->
      <section class="w-full max-w-md p-4 bg-white rounded-lg shadow-md">
        <h2 class="text-lg font-semibold text-gray-800 mb-4">Todo List</h2>
        <form on:submit={addTodo} class="flex space-x-2 mb-4">
          <input
            type="text"
            bind:value={newTodo}
            placeholder="New task"
            class="flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            required
          />
          <button
            type="submit"
            class="px-4 py-2 bg-green-600 text-white font-medium rounded-md hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-500"
          >
            Add
          </button>
        </form>
        <ul class="space-y-2">
          {#each todos as todo}
            <li class="flex justify-between items-center p-2 bg-gray-100 rounded-md">
              <span>{todo.task}</span>
              <button
                on:click={() => deleteTodo(todo.id)}
                class="text-red-600 hover:underline"
              >
                Delete
              </button>
            </li>
          {/each}
        </ul>
        <button
          on:click={logout}
          class="mt-4 w-full py-2 px-4 bg-red-600 text-white font-medium rounded-md hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500"
        >
          Logout
        </button>
      </section>
    {/if}
  </main>
  
  <style>
    main {
      font-family: 'Inter', sans-serif;
    }
  </style>
  