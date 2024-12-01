<svelte:head>
    <link rel="stylesheet" href="/src/todolist.css" />
</svelte:head>

<script lang="ts">
    import { onMount } from 'svelte';

    let tasks = [];

    let inputField: boolean = false;

    let taskTitle: string = '';

    onMount( async () => {
        const res = await fetch('http://localhost:8080/api/tasks/1');
        tasks = await res.json();
    });

    function sendCompletionUpdate(id: number, completion: boolean) {
        const res = fetch('http://localhost:8080/api/tasks/' + id + '/update?value=' + completion, {method: 'PUT'});
    }

    function sendDeletion(id: number) {
        const res = fetch('http://localhost:8080/api/tasks/delete/' + id, {method: 'DELETE'});
        tasks = tasks.filter((element) => element.id !== id);
    }

    async function addTask() {
        const res = await fetch('http://localhost:8080/api/tasks/1/new', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({title: taskTitle, completed: false})
        }).finally(() => {
            setInputField(false);

            window.location.reload();
        });
    }

    function setInputField(value: boolean) {
        inputField = value;
    }
</script>

{#key tasks.length}
    <div>
        {#each tasks as task}
            <div key="{task.id}" class="task">
                <input type="checkbox" bind:checked={task.completed} on:click={sendCompletionUpdate(task.id, this.checked)}>
                {task.title} ({task.user.name})
                <button class="deleteButton" on:click={sendDeletion(task.id)}>Delete</button>
            </div>
        {/each}
        <button on:click={() => setInputField(!inputField)}>+</button>

        {#if inputField === true}
            <div id="newTaskInput">
                <form>
                    <input type="text" bind:value={taskTitle}>
                    <button on:click={() => addTask()}>Add</button>
                </form>
            </div>
        {/if}
    </div>
{/key}
