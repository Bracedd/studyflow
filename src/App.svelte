<script>
    import { onMount } from 'svelte';
    import TaskManager from './components/TaskManager.svelte';
    import PomodoroTimer from './components/PomodoroTimer.svelte';
    import MoodTracker from './components/MoodTracker.svelte';
    import { slide } from 'svelte/transition';

    let currentTime = new Date();
    let activeSection = 'dashboard';

    onMount(() => {
        const timer = setInterval(() => {
            currentTime = new Date();
        }, 1000);

        return () => clearInterval(timer);
    });

    $: formattedTime = currentTime.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
    $: formattedDate = currentTime.toLocaleDateString([], { weekday: 'long', month: 'long', day: 'numeric' });

    function setActiveSection(section) {
        activeSection = section;
    }
</script>

<style>
    /* Base styles */
    .dashboard {
        display: grid;
        grid-template-columns: 300px 1fr;
        grid-template-rows: 1fr auto;
        min-height: 100vh;
        background: #f0f2f5;
        transition: grid-template-columns 0.3s ease; /* Ensure smooth transition when grid layout changes */
    }

    .sidebar {
        background: white;
        padding: 2rem;
        border-right: 1px solid #e5e7eb;
        display: flex;
        flex-direction: column;
        gap: 2rem;
        transition: transform 0.3s ease;
    }

    .logo {
        font-size: 1.75rem;
        font-weight: 700;
        color: #111827;
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .nav-menu {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }

    .nav-item {
        display: flex;
        align-items: center;
        gap: 0.75rem;
        padding: 0.75rem 1rem;
        border-radius: 8px;
        color: #6b7280;
        cursor: pointer;
        transition: all 0.2s;
        font-size: 1.1rem;
    }

    .nav-item:hover,
    .nav-item.active {
        background: #f3f4f6;
        color: #111827;
        font-weight: 600;
    }

    .datetime-card {
        background: #f9fafb;
        padding: 1.5rem;
        border-radius: 12px;
        text-align: center;
    }

    .time {
        font-size: 2.5rem;
        font-weight: 700;
        color: #111827;
        line-height: 1;
        margin-bottom: 0.5rem;
    }

    .date {
        color: #6b7280;
        font-size: 1rem;
    }

    .main-content {
        padding: 2rem;
        display: flex;
        flex-direction: column;
    }

    .header {
        margin-bottom: 2rem;
    }

    .welcome {
        font-size: 1.75rem;
        font-weight: 700;
        color: #111827;
        margin-bottom: 0.5rem;
    }

    .subtitle {
        font-weight: 500;
        color: #111827;
    }

    .grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
        gap: 1.5rem;
    }

    .card {
        background: white;
        border-radius: 12px;
        padding: 1.5rem;
        border: 1px solid #e5e7eb;
        transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
    }

    .card:hover {
        transform: scale(1.05); /* Slight zoom-in effect */
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); /* Add a subtle shadow on hover */
    }

    .footer {
        grid-column: 1 / -1;
        background: white;
        border-top: 1px solid #e5e7eb;
        padding: 0.5rem;
        text-align: center;
        color: #6b7280;
        font-size: 0.75rem;
    }

    .footer a {
        color: #111827;
        text-decoration: none;
    }

    .footer a:hover {
        text-decoration: underline;
    }

    /* Responsive Design */
    @media (max-width: 768px) {
        .dashboard {
            grid-template-columns: 1fr; /* Stack content vertically for mobile */
        }

        .sidebar {
            display: none; /* Hide sidebar on mobile */
        }

        .main-content {
            padding: 1rem;
        }

        .header {
            text-align: center;
        }

        .grid {
            grid-template-columns: 1fr; /* Stack cards vertically on mobile */
        }

        .nav-menu {
            display: none; /* Hide nav menu on mobile */
        }
    }

    /* Show sidebar on tablets and above */
    @media (min-width: 769px) {
        .dashboard {
            grid-template-columns: 300px 1fr; /* Show sidebar and main content side-by-side */
        }

        .sidebar {
            display: flex; /* Show sidebar on tablets and larger screens */
        }

        .nav-menu {
            display: block; /* Show nav menu on tablets and larger screens */
        }
    }
</style>

<div class="dashboard">
    <aside class="sidebar">
        <div class="logo">
            <i class='bx bx-book-reader'></i>
            StudyFlow
        </div>

        <div class="datetime-card">
            <div class="time">{formattedTime}</div>
            <div class="date">{formattedDate}</div>
        </div>

        <nav class="nav-menu">
            <div class="nav-item {activeSection === 'dashboard' ? 'active' : ''}" on:click={() => setActiveSection('dashboard')}>
                <i class='bx bx-home'></i>
                Dashboard
            </div>
            <div class="nav-item {activeSection === 'tasks' ? 'active' : ''}" on:click={() => setActiveSection('tasks')}>
                <i class='bx bx-list-check'></i>
                Tasks
            </div>
            <div class="nav-item {activeSection === 'mood' ? 'active' : ''}" on:click={() => setActiveSection('mood')}>
                <i class='bx bx-smile'></i>
                Mood Logs
            </div>
        </nav>
    </aside>

    <main class="main-content">
        {#if activeSection === 'dashboard'}
            <div in:slide={{ duration: 300 }}>
                <div class="header">
                    <h1 class="welcome">Welcome!</h1>
                    <p class="subtitle">Here's an overview of your study progress</p>
                </div>

                <div class="grid">
                    <div class="card">
                        <TaskManager />
                    </div>
                    <div class="card">
                        <PomodoroTimer />
                    </div>
                    <div class="card">
                        <MoodTracker />
                    </div>
                </div>
            </div>
        {/if}

        {#if activeSection === 'tasks'}
            <div class="card" in:slide={{ duration: 300 }}>
                <TaskManager />
            </div>
        {/if}

        {#if activeSection === 'mood'}
            <div class="card" in:slide={{ duration: 300 }}>
                <MoodTracker />
            </div>
        {/if}
    </main>

    <footer class="footer">
        <p>Made by <a href="https://github.com/bracedd" target="_blank">Divpreet</a> | <a href="https://github.com/bracedd/studyflow" target="_blank" rel="noopener noreferrer">Source Code</a></p>
    </footer>
</div>

