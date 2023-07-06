<script>
	// @ts-nocheck

	import { onMount } from 'svelte';

	let isLoading = false;
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
			isLoading = true;
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
					'https://register-form-asp-net-586743e95318.herokuapp.com/Line',
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

					// Update the isLoggedIn variable
					isLoggedIn = true;
					isLoading = false;
				} else {
					console.error('Request failed:', response.status);
					isLoading = false;
				}
			}
		} catch (error) {
			console.error(error);
		}
		try {
			const response = await fetch('https://register-form-asp-net-586743e95318.herokuapp.com/Tags');
			if (response.ok) {
				const res = await response.json();
				tags = res.data;
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

		try {
			const data = tags;
			const response = await fetch(
				'https://register-form-asp-net-586743e95318.herokuapp.com/Tags',
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
			} else {
				console.error('Request failed:', response.status);
			}
		} catch (error) {
			console.error('Error:', error.message);
		}
	}

	async function handleSubmit() {
		try {
			const formValues = tags.map((tag) => ({
				id: userProfile.userId,
				name: tag.name,
				value: tag.value
			}));

			// Send the form data to the API endpoint

			console.log(JSON.stringify(formValues));

			const response = await fetch(
				'https://register-form-asp-net-586743e95318.herokuapp.com/GoogleSheet',
				{
					method: 'POST',
					headers: {
						'Content-Type': 'application/json'
					},
					body: JSON.stringify(formValues)
				}
			);

			if (response.ok) {
				const data = await response.json();
				console.log('Response from API:', data);
				// Do something with the response from the API
			} else {
				throw new Error('API request failed');
			}
		} catch (error) {
			console.error('Error:', error);
			// Handle any errors that occur during the API call
		}
	}
</script>

<main class="container mx-auto px-4 bg-slate-200 rounded-3xl bg-opacity-60 min-h-screen py-4">
	{#if !isLoggedIn}
		<div class="flex justify-center items-center h-screen">
			<button
				class="py-4 px-10 bg-blue-500 text-white rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:shadow-outline-blue font-bold"
				class:animate-pulse={isLoading}
				disabled={isLoading}
				on:click={login}
			>
				{#if isLoading}
					<span class="animate-spin">Loading...</span>
				{:else}
					Login with Line
				{/if}
			</button>
		</div>
	{/if}

	{#if isLoggedIn}
		{#if userProfile.userId === 'U033d432e31846b2f496d79ee85cb639a' || userProfile.userId === 'Ue811014b8c8038ec716da73e4489c4db'}
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
					<div class="flex mb-4">
						<label for="tag-required" class="m-4 font-bold">Tag Required : </label>
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
		{/if}
		{#if tags.length > 0}
			<form class="mt-8" on:submit|preventDefault={handleSubmit}>
				<div class="flex flex-col w-[80%] mx-auto">
					{#each tags as tag}
						<div class="flex mb-4 flex-col">
							{#if tag.required}
								<label for={tag.name} class="text-lg font-bold text-gray-900"
									>{tag.name}:<span class="text-red-500 ml-2">*</span></label
								>
								{#if tag.type === 'checkbox'}
									<input
										type="checkbox"
										required
										bind:group={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'color'}
									<input
										type="color"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'date'}
									<input
										type="date"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'datetime-local'}
									<input
										type="datetime-local"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'email'}
									<input
										type="email"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'file'}
									<input
										type="file"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'hidden'}
									<input
										type="hidden"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'month'}
									<input
										type="month"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'number'}
									<input
										type="number"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'password'}
									<input
										type="password"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'radio'}
									<input
										type="radio"
										required
										bind:group={tag.value}
										value={tag.name}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'range'}
									<input
										type="range"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'reset'}
									<input
										type="reset"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'search'}
									<input
										type="search"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'submit'}
									<input
										type="submit"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'tel'}
									<input
										type="tel"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'text'}
									<input
										type="text"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'time'}
									<input
										type="time"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'url'}
									<input
										type="url"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'week'}
									<input
										type="week"
										required
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{/if}
							{:else}
								<label for={tag.name} class="text-lg font-bold text-gray-900">{tag.name}:</label>
								{#if tag.type === 'checkbox'}
									<input
										type="checkbox"
										bind:group={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'color'}
									<input
										type="color"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'date'}
									<input
										type="date"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'datetime-local'}
									<input
										type="datetime-local"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'email'}
									<input
										type="email"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'file'}
									<input
										type="file"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'hidden'}
									<input
										type="hidden"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'month'}
									<input
										type="month"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'number'}
									<input
										type="number"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'password'}
									<input
										type="password"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'radio'}
									<input
										type="radio"
										bind:group={tag.value}
										value={tag.name}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'range'}
									<input
										type="range"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'reset'}
									<input
										type="reset"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'search'}
									<input
										type="search"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'submit'}
									<input
										type="submit"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'tel'}
									<input
										type="tel"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'text'}
									<input
										type="text"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'time'}
									<input
										type="time"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'url'}
									<input
										type="url"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{:else if tag.type === 'week'}
									<input
										type="week"
										bind:value={tag.value}
										class="border border-gray-400 p-2 rounded-lg ml-2"
									/>
								{/if}
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
</main>
