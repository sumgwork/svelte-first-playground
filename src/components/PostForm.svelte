<script>

    import {createEventDispatcher} from "svelte";

    export let editingPost;

    const apiBaseUrl = "https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev";

    $: title=editingPost.title;
    $: body=editingPost.body;
    let loading = false;

    const dispatch = createEventDispatcher();

    const onSubmit = async (e) => {
        e.preventDefault();

        if(title.trim() === '' || body.trim() === ''){
            return;
        }
        loading = true;
        const newPost = {title, body};
        let URL, method;
        if(editingPost.id){
            URL = `${apiBaseUrl}/post/${editingPost.id}`; 
            method="PUT";
        } else {
            URL = `${apiBaseUrl}/post`; 
            method="POST";
        }

        const response = await fetch(URL, {
            method,
            body: JSON.stringify(newPost)
        })
        const post = await response.json()
        dispatch('postCreated', post)
        loading = false;
        editingPost.title=''
        editingPost.body=''
        editingPost.id=null
        
    }
</script>


{#if !loading}
<form on:submit={onSubmit}>
    <div class="input-field">
        <label for="title">Title</label>
        <input type="text" bind:value={editingPost.title} />
    </div>

    <div class="input-field">
        <label for="body">Body</label>
        <input type="text" bind:value={editingPost.body} />
    </div>

    <button class="waves-effect waves-light btn" type="submit">{editingPost.id? "Update": "Add"}</button>
</form>
{:else}
    <div class="progress">
        <div class="indeterminate"></div>
    </div>
{/if}

<style>
    form {
        margin: 50px;
    }
    .progress {
        margin: 100px 0;
    }
</style>