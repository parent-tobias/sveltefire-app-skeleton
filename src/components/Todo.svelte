<script>
  import { createEventDispatcher } from 'svelte';
  
  const dispatch = new createEventDispatcher();
  
  export let title,
             createdAt,
             done;
  
  const saveTodo = ()=>{
    
    if (!createdAt || createdAt=='') createdAt = new Date().toString();
    
    const todo = {
      title,
      createdAt,
      done
    }
    dispatch('save', {
      todo
    })
  }
  const deleteTodo = () =>{
    dispatch('delete', {
      createdAt
    })
  }
</script>

<span class='todo'>
  <span bind:textContent={title} contenteditable='true' class='title'>{title}</span> <label>Done: <input type='checkbox' value="done" on:click={()=>done=!done} bind:checked={done} /></label> <button on:click={saveTodo}>Save</button> {#if (createdAt)}<button on:click={deleteTodo}>Delete</button>{/if}
</span>

<style>
  .todo{
    display: flex;
    justify-content: space-between;
  }
</style>