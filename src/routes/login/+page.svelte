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
let s_username = ""
let s_password = ""

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

    await pb.collection('users').create(data)
    .catch((e) => alert("Invalid Something, make sure it is a real email"))
    .then(() => alert("SUCCESS, Now sign in"))
}


const login = async () => {
    try{
        await pb.collection('users').authWithPassword(s_username, s_password);
        alert("SUCESS")
    } catch{
        alert("Invalid Creds")
    } 

    // alert(pb.authStore.isValid)
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

            <input bind:value={s_username} type="text" placeholder="username" class="input input-bordered input-error w-full max-w-xs" />
            <input bind:value={s_password} type="text" placeholder="password" class="input input-bordered input-error w-full max-w-xs" />

            <button on:click={login} class="btn btn-outline">Login</button>
            
        </div>
    </div>
</div>