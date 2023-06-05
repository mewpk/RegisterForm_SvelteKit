<script>
	import { useState } from 'svelte';
	import Form from './Form.svelte';
  
	// Define an initial array of elements
	let elements = [
	  {
		label: 'Element 1',
		elementType: 'Type A'
	  },
	  {
		label: 'Element 2',
		elementType: 'Type B'
	  }
	];
  
	// Define a function to add a new element to the array
	function addElement(elementData) {
	  elements = [...elements, elementData];
	}
  
  </script>
  
  <main>
	<h1>My Elements</h1>
  
	<ul>
	  {#each elements as element}
		<li>{element.label} ({element.elementType})</li>
	  {/each}
	</ul>
  
	<!-- Display the form for adding a new element -->
	<Form onSave={addElement} />
  
  </main>
  