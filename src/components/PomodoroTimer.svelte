<script>
    import { onMount } from 'svelte';
    
    let minutes = 25;
    let seconds = 0;
    let interval;
    let isRunning = false;
    let isEditing = false;

    onMount(() => {
        const savedMinutes = localStorage.getItem('pomodoroMinutes');
        if (savedMinutes) {
            minutes = parseInt(savedMinutes);
        }
    });

    function startTimer() {
        if (isRunning) return;
        
        isRunning = true;
        interval = setInterval(() => {
            if (seconds === 0) {
                if (minutes === 0) {
                    clearInterval(interval);
                    isRunning = false;
                    alert('Time is up! Take a break.');
                    return;
                }
                minutes--;
                seconds = 59;
            } else {
                seconds--;
            }
        }, 1000);
    }

    function pauseTimer() {
        clearInterval(interval);
        isRunning = false;
    }

    function resetTimer() {
        clearInterval(interval);
        isRunning = false;
        minutes = parseInt(localStorage.getItem('pomodoroMinutes')) || 25;
        seconds = 0;
    }

    function handleTimeClick() {
        if (!isRunning) {
            isEditing = true;
        }
    }

    function handleTimeInput(event) {
        const newMinutes = parseInt(event.target.value) || 25;
        minutes = Math.min(Math.max(1, newMinutes), 60);
        localStorage.setItem('pomodoroMinutes', minutes.toString());
        isEditing = false;
    }
</script>

<style>
    .timer-container {
        color: #111827;
    }

    .timer-container.active {
        border-color: #111827;
    }

    h2 {
        font-size: 1.25rem;
        font-weight: 600;
        letter-spacing: -0.02em;
        margin-bottom: 1.5rem;
    }

    .timer-controls {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 1rem;
        margin-bottom: 1.5rem;
    }

    .time-display {
        font-size: 3.5rem;
        font-weight: 700;
        font-variant-numeric: tabular-nums;
        letter-spacing: -0.03em;
        cursor: pointer;
        transition: opacity 0.2s ease;
    }

    .time-display:hover {
        opacity: 0.8;
    }

    .time-input {
        font-size: 3.5rem;
        font-weight: 700;
        width: 120px;
        text-align: center;
        border: none;
        border-bottom: 2px solid #111827;
        background: transparent;
        padding: 0;
        margin: 0;
        color: #111827;
    }

    .time-input:focus {
        outline: none;
    }

    .time-input::-webkit-inner-spin-button,
    .time-input::-webkit-outer-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }
    
    .time-input[type=number] {
        -moz-appearance: textfield;
    }

    .controls {
        display: flex;
        gap: 0.75rem;
    }

    button {
        flex: 1;
        padding: 0.75rem 1.5rem;
        border-radius: 0.5rem;
        font-size: 0.875rem;
        font-weight: 500;
        transition: all 0.2s ease;
        border: 1px solid #e5e7eb;
        cursor: pointer;
    }

    .start {
        background: #111827;
        color: white;
        border-color: #111827;
    }

    .start:hover {
        background: #1f2937;
    }

    .pause {
        background: #fff;
        color: #111827;
        border-color: #111827;
    }

    .pause:hover {
        background: #f3f4f6;
    }

    .reset {
        background: #fff;
        color: #6b7280;
    }

    .reset:hover {
        background: #f3f4f6;
    }
</style>

<div class="timer-container" class:active={isRunning}>
    <h2>
        <i class='bx bx-time'></i>
        Pomodoro Timer
    </h2>
    <div class="timer-controls">
        {#if isEditing}
            <input
                type="number"
                class="time-input"
                value={minutes}
                min="1"
                max="60"
                on:blur={handleTimeInput}
                on:keydown={(e) => e.key === 'Enter' && handleTimeInput(e)}
                autofocus
            />
        {:else}
            <div class="time-display" on:click={handleTimeClick}>
                {String(minutes).padStart(2, "0")}:{String(seconds).padStart(2, "0")}
            </div>
        {/if}
    </div>
    <div class="controls">
        {#if isRunning}
            <button class="pause" on:click={pauseTimer}>
                <i class='bx bx-pause'></i> Pause
            </button>
        {:else}
            <button class="start" on:click={startTimer}>
                <i class='bx bx-play'></i> Start
            </button>
        {/if}
        <button class="reset" on:click={resetTimer}>
            <i class='bx bx-reset'></i> Reset
        </button>
    </div>
</div>