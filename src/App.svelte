<script>
  import logo from './assets/todo-icon.svg'
  import New from './lib/New.svelte'
  import { fade } from 'svelte/transition';
  import { writable } from 'svelte/store';

  let name
  let todos
  const nameStore = writable(location.pathname.replace('/', ''))
  nameStore.subscribe(n => { 
    name = n
  })


  const todosStore = writable(JSON.parse(window.localStorage.getItem(`todo/${name}`) || "[]"))

  todosStore.subscribe(val => { 
    todos = val
    window.localStorage.setItem(`todo/${name}`, JSON.stringify(todos))
  })
  
  /**
  * @param {string} name
  */
  function navigateToTodoList(name) {
    nameStore.set(name)
  }

  function removeItem(id) {
    todosStore.update(todos => todos.filter(todo => todo.id !== id))
  }

  function addNewItem() {
    todosStore.update(todos => [...todos, { id: todos.length, done: false, name: "", editing: true }])
  }

  function removeDone() {
    todosStore.update(todos => todos.filter( todo => !todo.done ))
  }

  async function addFromClipboard() {
    const text = await navigator.clipboard.readText();
    console.log(text)
    todosStore.update(todos => {
      let id = todos.length
      const newTodos = text.split('\n')
        .map(tx => tx.trim())
        .filter(tx => tx != "")
        .map(tx => { return { id: id++, done: false, name: tx} })

      return [...todos, ...newTodos]
    })
   
  }

  window.addEventListener('popstate', () => {
    nameStore.set(location.pathname.replace('/', ''))
  });

</script>

<main>
  {#if name === ""}
  <div transition:fade>
    <img transition:fade src={logo}  alt="Svelte Logo" />
    <h1>Yet Another Todo List</h1>
    <New  navigateToTodoList={navigateToTodoList} />
  </div>
  {:else}
  <div transition:fade class="todolist">
    <div class="todolist-header">
      <img transition:fade src={logo}  alt="Svelte Logo" on:click="{() => location.pathname = "/"}"/>
      <h1>{name}</h1>
    </div>
    <ul>
      {#each todos as item}
        <li
          class:done={item.done} 
          on:click="{() => { 
            if (item.editing) {
              return
            }
            item.done = !item.done
          }}"
          on:dblclick="{() => removeItem(item.id)}"
        >
          <input type="checkbox" bind:checked="{item.done}" />
          {#if item.editing}
            <form name="{item.id}" on:submit|preventDefault="{() => item.editing = false}">
              <input type="text" bind:value="{item.name}"/>
            </form>
          {:else}
            {item.name}
          {/if}
        </li>
      {/each}
    </ul>
    <div class="buttons">
      <input type="button" value="add" on:click="{addNewItem}"/>
      <input type="button" value="remove" on:click="{removeDone}"/>
      <input type="button" value="add from clipboard" on:click="{addFromClipboard}"/>
    </div>
  </div>
  {/if}
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  img {
    height: 16rem;
    width: 16rem;
  }

  div.todolist-header {
    margin: 0 0 0 0;
    text-align: left;
    display: inline-block;
    width: 100%;
  }
  div.todolist-header > img {
      margin: auto;
      float: left;
      height: 2rem;
      width: 2rem;
  }

  div.todolist-header > h1 {
    color: navy;
    text-transform: none;
    font-size: 1.5rem;
    font-weight: 300;
    float: left;
    line-height: 1.2;
    margin: 0rem 1rem;
    text-overflow: ellipsis;
    white-space: nowrap;  
  }

  div.todolist > ul {
    list-style-type: none;
    margin-top: 1rem;
    padding: 0;
    text-align: left;
    width: 100%;
    display: block;
  }

  div.todolist > ul > li {
    display: block;
    width: 100%;
    margin: 0.25rem;
    padding: 0.25rem;
    border-bottom: 2px solid lightskyblue;
    color: navy;
  }

  div.todolist > ul > li.done {
    text-decoration: line-through;
    color: lightskyblue;
  }

  div.todolist > ul > li > form {
    display: inline;
  }

  h1 {
    color: navy;
    text-transform: uppercase;
    font-size: 4rem;
    font-weight: 100;
    line-height: 1.1;
    margin: 2rem auto;
    max-width: 14rem;
  }

  @media (min-width: 480px) {
    h1 {
      max-width: none;
    }
  }
</style>
