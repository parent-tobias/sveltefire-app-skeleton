<script>
  import { getContext } from 'svelte';

  import Todo from './Todo.svelte';
  import NewTodo from './NewTodo.svelte';
  
  export let user;
  export let publicStore;
  export let privateStore;
  
    const saveTodo = (event)=>{
  const db = getContext('firebase').firestore();
    
    console.log(event.detail);
    
    const todo = event.detail.todo
    privateStore.collection('todos').doc(todo.createdAt).set(todo);
      
    db.collection('todos').doc(todo.createdAt).set(todo)
      .then(function() {
        console.log("Document successfully written!");
      })
      .catch(function(error) {
        console.error("Error writing document: ", error);
      });
  }
  
  const deleteTodo = (event)=>{
    const db = getContext('firebase').firestore();
    db.collection('todos').doc(event.detail.createdAt).delete()
      .then(function() {
        console.log("Document successfully Deleted!");
      })
      .catch(function(error) {
        console.error("Error writing document: ", error);
      });
  }
</script>
<section>
  <h2>{user.displayName}'s Todos</h2>
  <section>
    <NewTodo title='new todo' done='false' on:save={saveTodo} />

    {#each publicStore as todo}
      <Todo title={todo.title} done={todo.done} createdAt={todo.createdAt || new Date().toString() } on:save={saveTodo} on:delete={deleteTodo} />
    {/each}
  </section>
</section>
