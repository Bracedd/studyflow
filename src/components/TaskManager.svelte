<script>
    import { onMount } from 'svelte';
    import { slide } from 'svelte/transition';

    let tasks = [];
    let newTask = '';

    onMount(() => {
        const savedTasks = localStorage.getItem('tasks');
        if (savedTasks) {
            tasks = JSON.parse(savedTasks);
        }
    });

    function addTask() {
        if (newTask.trim()) {
            tasks = [...tasks, { text: newTask, completed: false }];
            newTask = '';
            saveTasks();
        }
    }

    function toggleTask(index) {
        tasks[index].completed = !tasks[index].completed;
        saveTasks();
    }

    function deleteTask(index) {
        tasks = tasks.filter((_, i) => i !== index);
        saveTasks();
    }

    function saveTasks() {
        localStorage.setItem('tasks', JSON.stringify(tasks));
    }

    $: completedTasks = tasks.filter(task => task.completed).length;
    $: totalTasks = tasks.length;
    $: progress = totalTasks ? (completedTasks / totalTasks) * 100 : 0;
</script>

<style>
    .task-container {
        color: #111827;
    }

    .header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 1rem;
    }

    h2 {
        margin: 0;
        font-size: 1.25rem;
        font-weight: 600;
    }

    .add-button {
        background: #111827;
        color: white;
        border: none;
        width: 32px;
        height: 32px;
        border-radius: 8px;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    input[type="text"] {
        width: 100%;
        padding: 0.75rem;
        border: 1px solid #dee2e6;
        border-radius: 8px;
        margin-bottom: 1rem;
        font-size: 1rem;
    }

    .task-list {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .task-item {
        display: flex;
        align-items: center;
        padding: 0.75rem;
        border-bottom: 1px solid #dee2e6;
    }

    .task-item:last-child {
        border-bottom: none;
    }

    input[type="checkbox"] {
        margin: 0;
        margin-right: 8px;
    }

    .task-text {
        flex: 1;
        font-size: 1rem;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin-right: 8px;
    }

    .task-item.completed .task-text {
        text-decoration: line-through;
        color: #6b7280;
    }

    .delete-btn {
        color: #dc3545;
        cursor: pointer;
        opacity: 0.7;
    }

    .delete-btn:hover {
        opacity: 1;
    }

    .progress-bar {
        height: 8px;
        background-color: #e5e7eb;
        border-radius: 4px;
        overflow: hidden;
        margin-bottom: 1rem;
    }

    .progress {
        height: 100%;
        background-color: #111827;
        transition: width 0.3s;
    }

    .quote {
        font-size: 1rem;
        font-style: italic;
        color: #6b7280;
        text-align: center;
        margin-top: 1rem;
        padding: 1rem 0;
    }
</style>

<div class="task-container">
    <div class="header">
        <h2>Today's Goals</h2>
        <button class="add-button" on:click={addTask}>
            <i class='bx bx-plus'></i>
        </button>
    </div>
    
    <input
        type="text"
        placeholder="Add a new task..."
        bind:value={newTask}
        on:keydown={e => e.key === 'Enter' && addTask()}
    />

    <div class="progress-bar">
        <div class="progress" style="width: {progress}%"></div>
    </div>

    <ul class="task-list">
        {#each tasks as task, i (task)}
            <li 
                class="task-item" 
                class:completed={task.completed}
                in:slide={{ duration: 300 }}
            >
                <input 
                    type="checkbox" 
                    checked={task.completed}
                    on:change={() => toggleTask(i)}
                />
                <span class="task-text">{task.text}</span>
                <i class='bx bx-x delete-btn' on:click={() => deleteTask(i)}></i>
            </li>
        {/each}
    </ul>

    <div class="quote">"The secret of getting ahead is getting started." - Mark Twain</div>
</div>