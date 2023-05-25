<script>
	import { onMount } from 'svelte';
	import axios from 'axios';

	let email = '';
	let password = '';

	const login = async () => {
		try {
			const response = await axios.post('/api/login', { email, password });
			console.log(response.data); // Assuming the server returns the user data
		} catch (error) {
			console.error(error);
		}
	};

	onMount(() => {
		// Load the Line SDK script asynchronously
		const script = document.createElement('script');
		script.src = 'https://static.line-scdn.net/liff/edge/2/sdk.js';
		script.async = true;
		script.onload = () => {
			// Initialize the Line SDK
			window.liff.init({ liffId: '1661230116-LkXrjd8N' });
		};
		document.body.appendChild(script);
	});

	const lineLogin = async () => {
		try {
			const line = window.liff;
			if (!line.isLoggedIn()) {
				line.login();
			} else {
				const userProfile = await line.getProfile();
				// Save the user profile to the datasheet or perform any other action
				console.log(userProfile);
				const data = {
					userId: userProfile.userId,
					displayName: userProfile.displayName,
					pictureUrl: userProfile.pictureUrl,
					statusMessage: userProfile.statusMessage
				};
				const response = await fetch(
					'https://script.google.com/macros/s/AKfycbw8Xei3EBCDanNkhGlBDX7GYHBTjA1dlo8mxmKdS6vTybjAxG-R4fnn51wm7kPKdResdQ/exec',
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
				} else {
					console.error('Request failed:', response.status);
				}
			}
		} catch (error) {
			console.error(error);
		}
	};
</script>

<main class="flex items-center justify-center min-h-screen bg-gray-100">
	<div class="max-w-md w-full space-y-8 p-6 bg-white rounded-lg shadow-lg">
		<h2 class="text-2xl font-bold text-center">Login</h2>

		<form class="space-y-6" on:submit|preventDefault={login}>
			<div>
				<label for="email" class="block font-medium text-gray-700">Email Address</label>
				<input
					type="email"
					id="email"
					class="form-input mt-1 block w-full"
					bind:value={email}
					required
				/>
			</div>

			<div>
				<label for="password" class="block font-medium text-gray-700">Password</label>
				<input
					type="password"
					id="password"
					class="form-input mt-1 block w-full"
					bind:value={password}
					required
				/>
			</div>

			<div>
				<button type="submit" class="btn btn-primary w-full">Login</button>
			</div>
		</form>

		<div>
			<button type="button" class="btn btn-line w-full" on:click={lineLogin}>Login with Line</button
			>
		</div>
	</div>
</main>

<style lang="postcss">
</style>
