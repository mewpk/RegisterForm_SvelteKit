<template>
    <div class="container mx-auto my-4 p-4 rounded-lg shadow-lg">
      <form on:submit|preventDefault={save}>
        <h2 class="text-xl font-bold mb-4">Add Element</h2>
        <div class="mb-4">
          <label for="element_type" class="block text-gray-700 font-bold mb-2">Element Type:</label>
          <select id="element_type" class="w-full py-2 px-3 rounded bg-gray-200 border border-transparent focus:outline-none focus:bg-white focus:border-gray-500" bind:value={selectedElementType}>
            {#each elementTypes as type}
              <option value={type}>{type}</option>
            {/each}
          </select>
        </div>
        <div class="mb-4">
          <label for="element_label" class="block text-gray-700 font-bold mb-2">Label:</label>
          <input type="text" id="element_label" class="w-full py-2 px-3 rounded bg-gray-200 border border-transparent focus:outline-none focus:bg-white focus:border-gray-500" bind:value={label} />
        </div>
        <div class="mb-4">
          <label for="element_name" class="block text-gray-700 font-bold mb-2">Name:</label>
          <input type="text" id="element_name" class="w-full py-2 px-3 rounded bg-gray-200 border border-transparent focus:outline-none focus:bg-white focus:border-gray-500" bind:value={name} />
        </div>
        <div class="mb-4">
          <label for="element_placeholder" class="block text-gray-700 font-bold mb-2">Placeholder:</label>
          <input type="text" id="element_placeholder" class="w-full py-2 px-3 rounded bg-gray-200 border border-transparent focus:outline-none focus:bg-white focus:border-gray-500" bind:value={placeholder} />
        </div>
        <div class="mb-6">
          <label for="element_required" class="inline-flex items-center">
            <input type="checkbox" id="element_required" class="form-checkbox h-5 w-5 text-gray-600" bind:checked={required} />
            <span class="ml-2 text-gray-700 font-bold">Required</span>
          </label>
        </div>
        <div class="flex items-center justify-between">
          <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">Save</button>
        </div>
      </form>
    </div>
  </template>
  
  <script>
    /**
     * @type {(arg0: { elementType: string; label: string; name: string; placeholder: string; required: boolean; }) => void}
     */
    export let onSave;
    let selectedElementType = 'text';
    let label = '';
    let name = '';
    let placeholder = '';
    let required = false;
  
    const elementTypes = ['text', 'email', 'password', 'number', 'date'];
  
    function save() {
      const elementData = {
        elementType: selectedElementType,
        label,
        name,
        placeholder,
        required
      };
  
      onSave(elementData);
  
      // Reset the input fields after saving
      label = '';
      name = '';
      placeholder = '';
      required = false;
    }
  </script>
  