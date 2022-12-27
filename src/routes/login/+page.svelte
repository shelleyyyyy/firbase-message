<script>

import { onMount } from 'svelte';

import PocketBase from 'pocketbase';
const pb = new PocketBase('http://170.187.151.26:8080');

onMount(() => {

    if (pb.authStore.isValid) {
        console.log("redirect")
    } else {
        console.log("stay")
    }
})

let username = ""
let email = ""
let password = ""
let name = ""


const createUser = async () => {
    // example create data
    const data = {
        "username": username,
        "email": email,
        "emailVisibility": true,
        "password": password,
        "passwordConfirm": password,
        "name": name
    };

    await pb.collection('users').create(data);
}


const login = async () => {
    const authData = await pb.collection('users').authWithPassword(username, password);

    // after the above you can also access the auth data from the authStore
    console.log(pb.authStore.isValid);
    console.log(pb.authStore.token);
    // console.log(pb.authStore.model.id);
}



</script>


<div class="grid grid-cols-2">

    <div>
        <h1 class="text-4xl font-bold text-center p-10">Sign Up</h1>

        <div class="grid grid-cols-1 justify-items-center gap-5">
            <input bind:value={username} type="text" placeholder="username" class="input input-bordered input-error w-full max-w-xs" />
            <input bind:value={email} type="text" placeholder="email" class="input input-bordered input-error w-full max-w-xs" />
            <input bind:value={password} type="text" placeholder="password" class="input input-bordered input-error w-full max-w-xs" />
            <input bind:value={name} type="text" placeholder="name" class="input input-bordered input-error w-full max-w-xs" />
        
            <button on:click={createUser} class="btn btn-outline">Sign Up</button>
        </div>
    </div>


    <div>
        <h1 class="text-4xl font-bold text-center p-10">Sign In</h1>

        <div class="grid grid-cols-1 justify-items-center gap-5">

            <input bind:value={username} type="text" placeholder="username" class="input input-bordered input-error w-full max-w-xs" />
            <input bind:value={password} type="text" placeholder="password" class="input input-bordered input-error w-full max-w-xs" />

            <button on:click={login} class="btn btn-outline">Login</button>
            
        </div>
    </div>
</div>