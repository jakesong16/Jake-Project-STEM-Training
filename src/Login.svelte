<script lang="ts">
    //import { currentUser, pb } from './pocketbase';
    //import PocketBase, { ClientResponseError } from 'pocketbase';
    import { writable } from 'svelte/store';
import PocketBase from 'pocketbase';
export const pocketbase = new PocketBase('http://127.0.0.1:8090');

export const currentUser = writable(pocketbase.authStore.model);

pocketbase.authStore.onChange((auth) => {
    console.log('authStore changed', auth);
    currentUser.set(pocketbase.authStore.model);
});

   
    //import PocketBase from 'pocketbase';

       // import { writable } from 'svelte/store';
        //export const pb = new PocketBase('http://127.0.0.1:8090');

    let email = '@mail.com' 
    let password = '';
  
    async function login() {
      const user = await pocketbase.collection('users').authWithPassword(email, password);
      console.log(user)
    }
  
    async function signUp() {
      //try {
        const data = {
          email,
          password,
          passwordConfirm: password,
          name: 'TestUser!',
        };
        const createdUser = await pocketbase.collection('users').create(data);
        await login();
      //} catch (err) {
       // const errorObj = err as ClientResponseError;
       // console.error(errorObj.message)
    }
  
    function signOut() {
      pocketbase.authStore.clear();
    }
  
  </script>
  
  {#if $currentUser}
    <p>
      Signed in as {$currentUser.email} 
      <button on:click={signOut}>Sign Out</button>
    </p>
  {:else}
    <form on:submit|preventDefault>
      <input
        placeholder="email"
        type="text"
        bind:value={email}
      />
  
      <input 
        placeholder="Password" 
        type="password" 
        bind:value={password} 
      />
      <button on:click={signUp}>Sign Up</button>
      <button on:click={login}>Login</button>
    </form>
  {/if}