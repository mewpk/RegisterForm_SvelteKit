<script>
	// @ts-nocheck

	import { onMount } from 'svelte';

	let isLoggedIn = false;
	let tags = [];
	let tagTypes = [
		'checkbox',
		'color',
		'date',
		'datetime-local',
		'email',
		'file',
		'hidden',
		'image',
		'month',
		'number',
		'password',
		'radio',
		'range',
		'reset',
		'search',
		'submit',
		'tel',
		'text',
		'time',
		'url',
		'week'
	];

	let selectedTagType;
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
					'https://d7a3-2001-fb1-a1-8604-c0b-1fae-e75b-79a0.ngrok-free.app/Line',
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
		try {
			const response = await fetch(
				'https://d7a3-2001-fb1-a1-8604-c0b-1fae-e75b-79a0.ngrok-free.app/Tags'
			);
			if (response.ok) {
				const res = await response.json();
				tags = res.data;
				console.log(tags);
			} else {
				console.error('Error:', response.status);
			}
		} catch (error) {
			console.error('Error:', error.message);
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

	async function addTag() {
		const tagNameInput = document.getElementById('tag-name');
		const tagRequired = document.getElementById('tag-required');
		const newTag = {
			name: tagNameInput.value,
			type: selectedTagType,
			required: tagRequired.checked
		};
		if (newTag.name) {
			tags = [...tags, newTag];
			tagNameInput.value = '';
			selectedTagType = null;
			tagRequired.checked = false;
		}
		console.log(tags);

		try {
			const data = tags;
			const response = await fetch(
				'https://d7a3-2001-fb1-a1-8604-c0b-1fae-e75b-79a0.ngrok-free.app/Tags',
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
		} catch (error) {
			console.error('Error:', error.message);
		}
	}
</script>

<main class="container mx-auto px-4 bg-slate-200 rounded-3xl bg-opacity-60 min-h-screen py-4">
	{#if !isLoggedIn}
		<div class="flex justify-center items-center h-screen ">
			<button
				class="py-4 px-10 bg-blue-500 text-white rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:shadow-outline-blue font-bold"
				on:click={login}>Login with Line</button
			>
		</div>
	{:else}
		<h1>{userProfile.userId}</h1>
	{/if}

	{#if isLoggedIn}
		{#if userProfile.userId === 'U033d432e31846b2f496d79ee85cb639a'}
			<form class="mt-8">
				<div class="flex flex-col w-[80%] mx-auto">

					<div class="flex flex-col mb-4">
				<label for="tag-type" class="m-4 font-bold">Tag Type:</label>
				<select id="tag-type" bind:value={selectedTagType} class="p-2 border rounded-lg mt-1">
					{#each tagTypes as tagType}
						<option value={tagType}>{tagType}</option>
					{/each}
				</select>
			</div>
			<div class="flex flex-col mb-4">
				<label for="tag-name" class="m-4 font-bold">Tag Name:</label>
				<input type="text" id="tag-name" class="p-2 border rounded-lg mt-1" />
			</div>
			<div class="flex flex-col mb-4">
				<label for="tag-required" class="m-4 font-bold">Tag Required:</label>
				<input type="checkbox" id="tag-required" class="p-2 border rounded-lg mt-1" />
			</div>
				<div class="flex justify-end">
					<button
						on:click={addTag}
						class="bg-gray-900 font-bold text-white py-2 px-4 rounded-lg mt-4 hover:bg-gray-700 focus:outline-none focus:shadow-outline-gray"
						>Add Tag</button
					>
				</div>
			</div>
			</form>

			{#if tags.length > 0}
				<form class="mt-8">
					<div class="flex flex-col w-[80%] mx-auto">
						{#each tags as tag}
							<div class="flex flex-col mb-4">
								{#if tag.required}
									<label for={tag.name} class=" text-lg font-bold text-gray-900"
										>{tag.name}<span class="text-red-500 ml-4">*</span></label
									>
									<input
										type={tag.type}
										required
										class="border border-gray-400 p-2 rounded-lg mt-2"
									/>
								{:else}
									<label for={tag.name} class="text-lg font-bold text-gray-900">{tag.name}</label>

									<input type={tag.type} class="border border-gray-400 p-2 rounded-lg mt-2" />
								{/if}
							</div>
						{/each}
						<input
							type="submit"
							class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
						/>
					</div>
				</form>
			{/if}
		{/if}
	{/if}
</main>
