<script>
  export let navigateToTodoList
  let slug = ""
  const handleSubmit = () => {
    history.pushState(null, null, slug)
    navigateToTodoList(slug)
  }
</script>


<form on:submit|preventDefault="{handleSubmit}">
  <input placeholder="your-todo-list-name" name="name" bind:value="{slug}" type="text" required/>
  <button type="submit">Go!</button>
</form>

<style>

  button, input {
    color: black;
    font-size: 2rem;
    font-weight: 100;
    line-height: 1.1;
    margin: 1rem auto;
  }

  input {
    min-width: 10rem;
    max-width: 40rem;
  }
  
  @media (min-width: 480px) {
    
  }
</style>

