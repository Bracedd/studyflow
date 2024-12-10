<script>
    import { onMount } from 'svelte';
    import { flip } from 'svelte/animate';
    import { fade, slide } from 'svelte/transition';

    let moods = [
        { emoji: '😊', label: 'Happy' },
        { emoji: '🤔', label: 'Focused' },
        { emoji: '😴', label: 'Tired' },
        { emoji: '😫', label: 'Stressed' },
        { emoji: '🎯', label: 'Motivated' }
    ];

    let selectedMood = moods[0];
    let note = '';
    let moodLogs = [];

    onMount(() => {
        const savedLogs = localStorage.getItem('moodLogs');
        if (savedLogs) {
            moodLogs = JSON.parse(savedLogs);
        }
    });

    function saveMood() {
        if (note.trim()) {
            const newLog = { mood: selectedMood, note, date: new Date().toLocaleString() };
            moodLogs = [newLog, ...moodLogs];
            localStorage.setItem('moodLogs', JSON.stringify(moodLogs));
            note = '';
        }
    }

    function deleteLog(index) {
        moodLogs = moodLogs.filter((_, i) => i !== index);
        localStorage.setItem('moodLogs', JSON.stringify(moodLogs));
    }
</script>

<style>
    .mood-card {
        color: #111827;
    }

    h2 {
        margin-top: 0;
        font-size: 1.25rem;
        font-weight: 600;
    }

    .mood-selector {
        margin: 1rem 0;
    }

    select {
        width: 100%;
        padding: 0.75rem;
        font-size: 1rem;
        border: 1px solid #dee2e6;
        border-radius: 8px;
        background-color: white;
    }

    textarea {
        width: 100%;
        height: 80px;
        margin: 1rem 0;
        padding: 0.75rem;
        border: 1px solid #dee2e6;
        border-radius: 8px;
        font-size: 1rem;
        resize: vertical;
    }

    button {
        width: 100%;
        padding: 0.75rem;
        font-size: 1rem;
        cursor: pointer;
        background-color: #111827;
        color: white;
        border: none;
        border-radius: 8px;
    }

    button:hover {
        background-color: #333;
    }

    .log-list {
        margin-top: 1rem;
        list-style: none;
        padding: 0;
    }

    .log-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0.75rem;
        border-bottom: 1px solid #dee2e6;
    }

    .log-item:last-child {
        border-bottom: none;
    }

    .log-date {
        font-size: 0.875rem;
        color: #6b7280;
        margin-top: 0.25rem;
    }

    .log-note {
        font-weight: 600;
        margin-top: 0.5rem;
    }

    .delete-btn {
        color: #dc3545;
        cursor: pointer;
        opacity: 0.7;
    }

    .delete-btn:hover {
        opacity: 1;
    }
</style>

<div class="mood-card">
    <h2>How's your study session?</h2>
    <div class="mood-selector">
        <select bind:value={selectedMood}>
            {#each moods as mood}
                <option value={mood}>
                    {mood.emoji} {mood.label}
                </option>
            {/each}
        </select>
    </div>
    <textarea bind:value={note} placeholder="Add a note about your study session..."></textarea>
    <button on:click={saveMood}>Log Study Mood</button>

    <ul class="log-list">
        {#each moodLogs as log, i}
            <li class="log-item">
                <div>
                    <div>{log.mood.emoji} {log.mood.label}</div>
                    <div class="log-note">{log.note}</div>
                    <div class="log-date">{log.date}</div>
                </div>
                <i class='bx bx-x delete-btn' on:click={() => deleteLog(i)}></i>
            </li>
        {/each}
    </ul>
</div> 