<script>
	import { auth } from '$lib/firebase/firebase.js';
	import { signInWithEmailAndPassword } from 'firebase/auth';
	import { Input, Label } from 'flowbite-svelte';
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';
	
  
	export let email = "";
	export let password = "";
	export let errorMessage = ""; 
	let user = null;
	let showPassword = false; // Track whether to show the password
  
	// Check for and load session data when the component mounts
	 onMount(() => {
    const userData = localStorage.getItem('user');

    if (userData) {
      const user = JSON.parse(userData);
      auth.currentUser = user;
      goto('/Homepage'); // Navigate to the homepage if a user is already logged in
    }
    // No user data found, do nothing (login page will be shown)
  });


	async function handleSubmit() {
	  try {
		const userCredential = await signInWithEmailAndPassword(auth, email, password);
		const user = userCredential.user;
  
		// Store user information in localStorage for persistent session
		localStorage.setItem('user', JSON.stringify(user));
  
		errorMessage = "";
  
		// Navigate to the /Homepage route after successful login
		goto('/Homepage');
	  } catch (error) {
		// Display error message if login fails
		errorMessage = "Invalid username or password.";
		console.error("Error logging in:", error.message);
	  }
	}
  
	const togglePasswordVisibility = () => {
	  // Toggle the password visibility
	  showPassword = !showPassword;
	}

	
  </script>
  {#if user}
  <p>Redirecting to homepage...</p>
{:else}
  
  <h1 style="color: blue;" class="h1">Login form</h1>
  <form on:submit={handleSubmit} class="container">
	<div class="imgcontainer">
	  <img src="https://recruitlycdn.com/assets/v3/login400x.png" alt="Avatar" class="avatar">
	</div>
  
	<div class="mb-6">
	  <Label for="email" class="form-label">Email:</Label>
	  <Input type="email" bind:value={email} required id="email" class="form-input" placeholder="Enter Email"  />
	</div>
  
	<div class="mb-6">
	  <div class="password-container">
		<Label for="password" class="form-label">Password:</Label>
		<Input
		  type={showPassword ? "text" : "password"} 
		  bind:value={password}
		  required
		  id="password"
		  placeholder="Enter Password"
		  class="form-input"
		/>
		<label class="password-toggle">
		  <input
			type="checkbox"
			checked={showPassword}
			on:change={togglePasswordVisibility}
		  />
		  Show Password
		</label>
	  </div>
	</div>
  
	{#if errorMessage}
	<p style="color: red;">{errorMessage}</p>
	{/if}
  
	<div class="mb-6">
	  <button type="button" on:click={handleSubmit} >Login</button>
	</div>
  
	<div class="mb-6">
	  <p>Please click on Register to create a new account.</p>
	  <a href="/register" style="color: blue; font-weight: bold; font-size: 18px; ">New User? Register</a>
	</div>
  </form>
 
  {/if}
  <style>
	.h1 {
	  margin-left: 500px;
	  margin-top: 35px;
	  font-weight: bold;
	  font-size: 30px;
	}
  
	.mb-6 {
	  border-radius: 1.25rem; 
	}
  
	.imgcontainer {
	  text-align: center;
	  margin: 24px 0 12px 0;
	  margin-left: 150px;
	}
  
	img.avatar {
	  width: 40%;
	  border-radius: 50%;
	}
  
	.container {
	  padding: 16px;
	  width: 500px;
	  background-color: rgb(238, 240, 234);
	  margin-left: 500px;
	  margin-top: 30px;
	}
  
	button {
	  background-color: #04AA6D;
	  color: white;
	  padding: 14px 20px;
	  margin: 8px 0;
	  border: none;
	  cursor: pointer;
	  width: 100%;
	}
  
	button:hover {
	  opacity: 0.8;
	}
  </style>