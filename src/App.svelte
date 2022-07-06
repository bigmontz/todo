<script>
  import logo from './assets/todo-icon.svg'
  import New from './lib/New.svelte'
  import { fade } from 'svelte/transition';
  import { writable } from 'svelte/store';

  let name
  const nameStore = writable(location.pathname.replace('/', ''))
  nameStore.subscribe(n => { 
    name = n
  })
  
  /**
  * @param {string} name
  */
  function navigateToTodoList(name) {
    nameStore.set(name)
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
    <img transition:fade src={logo}  alt="Svelte Logo" />
    <h1>{name}</h1>
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

  div.todolist {
    margin: 0 0 0 0;
    text-align: left;
  }
  div.todolist > img {
      margin: auto;
      float: left;
      height: 2rem;
      width: 2rem;
  }

  div.todolist > h1 {
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
