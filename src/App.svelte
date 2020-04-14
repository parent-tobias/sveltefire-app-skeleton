<script>
  import { FirebaseApp, User, Doc, Collection } from "sveltefire";
  import { getContext } from 'svelte';
  
  import Todos from './components/Todos.svelte';
  import Card from './components/Card.svelte';
  import WeatherWatcher from './components/WeatherWatcher.svelte';

  import firebase from "firebase/app";
  import "firebase/firestore";
  import "firebase/auth";
  import "firebase/performance";
  import "firebase/analytics";

  var firebaseConfig = {
    apiKey: "AIzaSyAtOBqNk79yVmabxw4FTZK9qbFTCz1_v08",
    authDomain: "mysandbox-44c2a.firebaseapp.com",
    databaseURL: "https://mysandbox-44c2a.firebaseio.com",
    projectId: "mysandbox-44c2a",
    storageBucket: "mysandbox-44c2a.appspot.com",
    messagingSenderId: "706675163032",
    appId: "1:706675163032:web:d2bf5625cc17e1f10b35ed"
  };

  firebase.initializeApp(firebaseConfig);  

</script>

<main>

  {#if !firebaseConfig.projectId}
    <strong>Step 0</strong>
    Create a
    <a href="https://firebase.google.com/">Firebase Project</a>
    and paste your web config into
    <code>App.svelte</code>
    .
  {/if}

  <!-- 1. 🔥 Firebase App -->
  <FirebaseApp {firebase}>
    <!-- 2. 😀 Get the current user -->
    <User let:user let:auth>
      
    <header>
      <figure>
        <img class='avatar'src={user.photoURL} alt={`pic of ${user.displayName}`} />
        <figcaption>{user.displayName}</figcaption>
      </figure>
      <h1>
        {user.displayName} user space
      </h1>

        <button on:click={() => auth.signOut()}>Sign Out</button>
    </header>

        <div slot="signed-out">
          <header>
            <h1>Welcome to the Svelte todo app!</h1>
            <h2>Log in below to see it working...</h2>
          </header>
          <button on:click={() =>{
            let provider = new firebase.auth.GoogleAuthProvider();
            auth.signInWithPopup(provider);
            } }>
            <img src="https://developers.google.com/identity/images/btn_google_signin_dark_normal_web.png" alt='google signin button' />
          </button>
        </div>
      <Collection path={`todos`} 
                  query={(ref)=>ref.orderBy('done')}
                  let:data={todos} 
                  let:ref={todosRef} 
                  log>
        <Todos {user} publicStore={todos} privateStore={firebase.firestore().collection('users').doc(user.uid).collection('apps').doc('Todos') } />
      </Collection>
      <Doc path={`users/${user.uid}/apps/WeatherWatcher`}
           let:data={privateStore}
           let:ref={weatherWatcherRef}
           >

        <WeatherWatcher {user} {privateStore} />
      </Doc>
      
    
    </User>
  </FirebaseApp>

</main>


<!-- Styles -->
<style>
  header {
    display: flex;
    justify-content: space-around;
    border-bottom: 1px silver;
  }
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1,
  em {
    color: #ff3e00;
  }

  hr {
    height: 1px;
    border: none;
    background: rgb(195, 195, 195);
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  .avatar {
    width: 64px;
    height: 64px;
    border-radius: 31px;
  }
</style>