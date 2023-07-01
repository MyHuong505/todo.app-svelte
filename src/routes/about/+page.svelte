<svelte:head>
	<title>About</title>
	<meta name="description" content="About this app" />
</svelte:head>

<script>

  let todos = [];
  let newTodo = "";
  let showPopup = false;

//GET
 const fetchTodo = async () => {
    const response = await fetch("http://localhost:3000/todos");
    todos = await response.json();
    console.log(todos);
  }
  fetchTodo();


// POST	
const createTodo = async () => {
  if (newTodo === "") {
    alert('Enter your task');
  }
  else {
    await fetch("http://localhost:3000/todos", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        description: newTodo,
        completed: false,
        isImportant: false
      })	
    });
    newTodo = "";
	fetchTodo();
  }
};


//PUT
const updateTodo = async(id, data) => {
	await fetch (`http://localhost:3000/todos/${id}`, {
		method: "PUT",
		headers: {
        	"Content-Type": "application/json",
      	},
    	body: JSON.stringify(data),
	})
}

//DELETE 
const deleteTodo = (id) => {
	fetch (`http://localhost:3000/todos/${id}`, {
		method: 'DELETE'
	})
	fetchTodo();
}

//EDIT
  const editedTodo = async () => {
    const editedTask = {
      id: editedId, 
      description: editedDescription,
      completed: false,
      isImportant: false
    };
    await updateTodo(editedTask.id, editedTask);
    showPopup = false;
    fetchTodo(); 
  };

let editedId = "";
let editedDescription = '';


</script>

<h1>Todo List</h1>

<ul>
  {#each todos as toDo}
    <li>
		<input type="checkbox" bind:checked={toDo.completed} on:change={()=> updateTodo(toDo.id, toDo)}/>
		<span>{toDo.description}</span>
		<button class="delete-button" on:click={()=> deleteTodo(toDo.id)}>x</button>
		{#if !toDo.completed}
		<button class="edit-button" on:click={() => {editedDescription = toDo.description, editedId = toDo.id, showPopup = true }}>
			Edit</button>
		{/if}
	</li>	
  {/each}
  {#if showPopup}
 <div class="overlay"> 
  <div class="popup">
    <input type="text" bind:value={editedDescription} />

    <button on:click={() => editedTodo()}>Save</button>
    <button on:click={() => { showPopup = false; }}>Cancel</button>
  </div>
 </div>
{/if}
</ul>

<input type="text" bind:value={newTodo} placeholder="New Todo" />
<button class="add-button" on:click={createTodo}>Add</button>








<style>

  ul li {
    list-style: none;
	position: relative;
  }

  .delete-button {
	padding: 0 8px;
	position: absolute;
	right: 64px;
    background: transparent;
    border: none;
    outline: none;
    cursor: pointer;
    font-size: 16px;
  }

  .edit-button {
	position: absolute;
	right: 100px;
	background: transparent;
    border: none;
    outline: none;
    cursor: pointer;
    font-size: 16px;
  }

ul li input[type="checkbox"]:checked + span {
	text-decoration: line-through;
	text-decoration-color: rgba(0, 0, 0, 0.5);

  }

button.add-button {
  margin: 16px 0;
  border: none;
  outline: none;
  padding: 8px;
  background: #ff5945;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
  border-radius: 40px;
}

/* 
  .popup {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    padding: 16px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    z-index: 9999;
  }


  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 9998;
  } */

</style>