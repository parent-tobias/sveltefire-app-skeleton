<script>
  import { createEventDispatcher } from 'svelte';
  import Todo from './Todo.svelte';
  
  export let title = "New todo",
             createdAt=undefined,
             done=false;
  
  const dispatch = createEventDispatcher();

  const saveTodo = (event)=>{
    const todo = event.detail.todo;
    
    dispatch('save', {
      todo
    })
    title="New todo";
    createdAt = undefined;
    done=false;
  }

</script>

<Todo bind:title={title} bind:createdAt={createdAt} bind:done={done} on:save={saveTodo} on:delete />