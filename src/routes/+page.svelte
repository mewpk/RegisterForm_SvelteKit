<script>
  // Define an initial array of elements
  let elements = [
	{
	  id: 1,
	  label: "Element 1",
	  elementType: "text",
	  editable: true,
	},
	{
	  id: 2,
	  label: "Element 2",
	  elementType: "number",
	  editable: true,
	},
  ];

  // Create a reactive variable to hold the form inputs
  let label = "";
  let elementType = "text";
  let modalOpen = false;
  /**
	 * @type {number}
	 */
  let indexToEdit;

  function addElement() {
	// Determine the highest existing id value
	const highestId = Math.max(...elements.map((element) => element.id));

	// Create a new element object with the values from the form inputs and a unique id
	const newElement = {
	  id: highestId + 1,
	  label,
	  elementType,
	  editable: true,
	};

	// Add the new element to the elements array
	elements = [...elements, newElement];

	// Clear the form inputs
	label = "";
	elementType = "text";
  }

  /**
	 * @param {number} index
	 */
  function editElement(index) {
	// Get the existing element object
	const element = elements[index];

	// Update the properties of the existing element object with the values from the form inputs
	element.label = label;
	element.elementType = elementType;

	// Clear the form inputs
	label = "";
	elementType = "text";

	// Close the modal
	modalOpen = false;
  }
</script>

<main class="container mx-auto">
  <h1 class="text-2xl font-bold mb-4">My Elements</h1>
  <div>
	<ul>
	  {#each elements as element, index}
		<li class="mb-4" >
		  <label for={element.label} class="font-bold mb-2">{element.label}:</label>
		  <input type={element.elementType} class="w-full border border-gray-400 p-2 rounded-lg" id={element.id.toString()} />

		  {#if element.editable}
			<button
			  type="button"
			  class="ml-4 bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
			  on:click={() => {
				label = element.label;
				elementType = element.elementType;
				modalOpen = true;
				indexToEdit = index;
			  }}
			>
			  Edit
			</button>
		  {/if}
		</li>
	  {/each}
	</ul>
  </div>

  <form class="mt-4">
	<div class="flex flex-col mb-4">
	  <label for="label" class="font-bold mb-2">Label:</label>
	  <input id="label" type="text" class="w-full border border-gray-400 p-2 rounded-lg" bind:value={label} />
	</div>

	<div class="flex flex-col mb-4">
	  <label for="elementType" class="font-bold mb-2">Element Type:</label>
	  <select id="elementType" class="w-full border border-gray-400 p-2 rounded-lg" bind:value={elementType}>
		<option value="text">Text</option>
		<option value="number">Number</option>
		<option value="email">Email</option>
		<option value="password">Password</option>
	  </select>
	</div>

	<button type="button" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" on:click={addElement}>Add Element</button>
  </form>

  <div>
	{#if modalOpen}
	  <div class="fixed inset-0 bg-gray-900 bg-opacity-50 flex justify-center items-center">
		<div class="bg-white rounded-lg p-8">
		  <h2 class="text-xl font-bold mb-4">Edit Element</h2>

		  <form>
			<div class="flex flex-col mb-4">
			  <label for="editLabel" class="font-bold mb-2">Label:</label>
			  <input id="editLabel" type="text" class="w-full border border-gray-400 p-2 rounded-lg" bind:value={label} />
			</div>

			<div class="flex flex-col mb-4">
			  <label for="editElementType" class="font-bold mb-2">Element Type:</label>
			  <select id="editElementType" class="w-full border border-gray-400 p-2 rounded-lg" bind:value={elementType}>
				<option value="text">Text</option>
				<option value="number">Number</option>
				<option value="email">Email</option>
				<option value="password">Password</option>
			  </select>
			</div>

			<button type="button" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" on:click={() => editElement(indexToEdit)}>Save Changes</button>
		  </form>
		</div>
	  </div>
	{/if}
  </div>
</main>
