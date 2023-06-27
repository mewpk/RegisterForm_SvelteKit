<script>
	import { onMount } from 'svelte';
	import axios from 'axios';

	let email = '';
	let password = '';


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

		try {
			// @ts-ignore
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
				} else {
					console.error('Request failed:', response.status);
				}
			}
		} catch (error) {
			console.error(error);
		}
	});
</script>