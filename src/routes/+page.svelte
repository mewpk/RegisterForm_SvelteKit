<script>
	import { onMount } from 'svelte';

	let isLoggedIn = false;
	/**
	 * @type {{ userId: any; displayName: any; pictureUrl: any; statusMessage: any; }}
	 */
	let userProfile;
	async function login() {
	  try {
		// @ts-ignore
		const line = window.liff;
		if (!line.isLoggedIn()) {
		  line.login();
		} else {
		  userProfile = await line.getProfile();
		  // Save the user profile to the datasheet or perform any other action
		  console.log(userProfile);
		  const data = {
			userId: userProfile.userId,
			displayName: userProfile.displayName,
			pictureUrl: userProfile.pictureUrl,
			statusMessage: userProfile.statusMessage
		  };
  
		  const response = await fetch(
			'https://5afc-2001-fb1-a1-8604-7cd0-dcc1-6ff4-5bd7.ngrok-free.app/Line',
			{
			  method: 'POST',
			  headers: {
				'Content-Type': 'application/json'
			  },
			  body: JSON.stringify(data)
			}
		  );
  
		  if (response.ok) {
			const responseData = await response.json();
			console.log(responseData);
  
			// Update the isLoggedIn variable
			isLoggedIn = true;
		  } else {
			console.error('Request failed:', response.status);
		  }
		}
	  } catch (error) {
		console.error(error);
	  }
	}
  
	onMount(async () => {
	  // Load the Line SDK script asynchronously
	  const script = document.createElement('script');
	  script.src = 'https://static.line-scdn.net/liff/edge/2/sdk.js';
	  script.async = true;
	  script.onload = () => {
		// Initialize the Line SDK
		// @ts-ignore
		window.liff.init({ liffId: '1661230116-LkXrjd8N' });
	  };
	  document.body.appendChild(script);
	});
  </script>
  
  <main class="flex flex-col h-screen justify-center items-center">
	
	{#if !isLoggedIn}
	  <button class="py-2 px-4 bg-blue-500 text-white rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:shadow-outline-blue" on:click={login}>Login with Line</button>
	{:else}
	<h1>
		{userProfile.userId}
	</h1>
	  <form>
		<!-- Add your form fields here -->
	  </form>
	{/if}
  </main>
  
  