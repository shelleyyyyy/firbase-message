<script>
    import PocketBase from 'pocketbase';
    import { onMount, onDestroy } from "svelte";
    const pb = new PocketBase('http://170.187.151.26:8080');

    let messages = []
    let unsubscribe = () => {}

    onMount(async () => {
        const records = await pb.collection('messages').getFullList(200 /* batch size */, {
            sort: '-created',
        });

        messages = records
        console.log(messages)

        unsubscribe = await pb
        .collection('messages')
        .subscribe('*', async ({ action, record }) => {
            if (action === 'create') {
            // Fetch associated user
            const user = await pb.collection('users').getOne(record.user);
            record.expand = { user };
            messages = [ record, ...messages];
            }
            if (action === 'delete') {
            messages = messages.filter((m) => m.id !== record.id);
            }
        });
    })

    // Unsubscribe from realtime messages
    onDestroy(() => {
        unsubscribe?.();
    });

    let message = ""

    const createMessage = async () => {

        console.log(pb.authStore.isValid);
        console.log(pb.authStore.token);
        console.log(pb.authStore.model);


        // example create data
        const data = {
            "message": message,
            "user": pb.authStore.model.id
        };

        if (pb.authStore.isValid){
            const record = await pb.collection('messages').create(data);
        } else {
            alert("MUST BE AUTHENTICATED TO SEND A MESSAGE")
        }
    }

</script>

<div class="mx-96">

    <div class="grid justify-items-center">
        <div class="flex gap-5">
            <input bind:value={message} type="text" placeholder="Type here" class="input input-bordered input-primary w-full max-w-xs" />
            <button class="btn btn-primary" on:click={createMessage}>Send</button>
        </div>
    </div>
    <div>
        {#each messages as m}
            {#if m.user == pb.authStore.model.id}
                <div class="chat chat-end">
                    <div class="chat-image avatar">
                    </div>
                    <div class="chat-header">
                    {m.user}
                    </div>
                    <div class="chat-bubble">{m.message}</div>
                </div>
            {:else}
                <div class="chat chat-start">
                    <div class="chat-image avatar">
                    </div>
                    <div class="chat-header">
                    {m.user}
                    </div>
                    <div class="chat-bubble">{m.message}</div>
                </div>
            {/if}
        {/each} 
    </div>
</div>