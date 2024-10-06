<script>
    import { onMount } from 'svelte';

    onMount(() => {
        loadCookies();
    });

    let tasks = [];
    let input_text = "";

    function addTask() {
        if (input_text.length > 0) {
            tasks = [...tasks, input_text];
            input_text = "";
            console.log(tasks);
            saveCookies();
        }
    }

    function removeTask(index) {
        tasks.splice(index, 1);
        tasks = [...tasks]; // Ensure reactivity
        saveCookies();
    }

    function saveCookies() {
        try {
            const encodedTasks = encodeURIComponent(JSON.stringify(tasks));  
            const expirationDate = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000); 
            document.cookie = `tasks=${encodedTasks}; path=/; expires=${expirationDate.toUTCString()}`;
            console.log("Saved cookie:", document.cookie);  
        } catch (e) {
            console.error("Error saving cookies:", e);  
        }
    }

    function loadCookies() {
        try {
            let cookies = document.cookie
                .split(";")
                .map((cookie) => cookie.trim().split("="))
                .reduce((acc, [key, value]) => ({ ...acc, [key]: value }), {});

            console.log("Cookies found:", cookies);

            if (cookies.tasks) {
                const decodedTasks = decodeURIComponent(cookies.tasks);
                console.log("Decoded tasks:", decodedTasks);

                tasks = JSON.parse(decodedTasks);
                console.log("Tasks loaded from cookies:", tasks);
            } else {
                console.log("No tasks cookie found");
            }
        } catch (e) {
            console.error("Error loading tasks from cookies:", e);
        }
    }
</script>

<body>
<section>
	<h1 class="title">To-do list</h1>
	<div id="container" class="container">
		<div class="input-container">
			<input
				type="text"
				id="input"
				bind:value={input_text}
				placeholder="Enter a task"
			/>
			<button id="add-btn" on:click={addTask}>Add</button>
		</div>
		<div class="task-container">
            {#each tasks as task, index}
                <div class="task">
                    <div class="task-text">{task}</div>
                    <div class="task-btns">
                        <button on:click={() => removeTask(index)}>X</button>
                    </div>
                </div>
            {/each}
        </div>
	</div>
</section>
</body>
<style>
	/* Overall Section Styling */
	body {
        margin:0;
        width: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
		background: linear-gradient(135deg, #6a11cb, #2575fc);
		font-family: 'Arial', sans-serif;
		color: #fff;
	}

	/* Title */
	h1.title {
		font-size: 2.5rem;
		margin-bottom: 20px;
		border-bottom: 2px solid rgba(255, 255, 255, 0.2);
		padding-bottom: 10px;
		text-transform: uppercase;
	}

	/* Container */
	.container {
		background-color: rgba(255, 255, 255, 0.1);
		padding: 20px;
		border-radius: 15px;
		width: 80vh;
		max-width: 600px;
		box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
	}

	/* Input container */
	.input-container {
		display: flex;
		margin-bottom: 20px;
	}

	.input-container input {
		flex: 1;
		padding: 10px;
		border-radius: 8px;
		border: none;
		outline: none;
		background-color: rgba(255, 255, 255, 0.2);
		color: #fff;
		font-size: 1rem;
		margin-right: 10px;
		transition: background-color 0.3s ease;
	}

	.input-container input::placeholder {
		color: #ddd;
	}

	.input-container input:focus {
		background-color: rgba(255, 255, 255, 0.3);
	}

	.input-container button {
		padding: 10px 15px;
		border: none;
		border-radius: 8px;
		background-color: #2575fc;
		color: #fff;
		cursor: pointer;
		font-size: 1rem;
		transition: background-color 0.3s ease;
	}

	.input-container button:hover {
		background-color: #6a11cb;
	}

	/* Task container */
	.task-container {
		display: flex;
		flex-direction: column;
		gap: 10px;
	}

	.task {
		display: flex;
		justify-content: space-between;
		align-items: center;
		background-color: rgba(255, 255, 255, 0.2);
		padding: 10px;
		border-radius: 8px;
		transition: background-color 0.3s ease, transform 0.2s ease;
	}

	.task:hover {
		background-color: rgba(255, 255, 255, 0.3);
		transform: translateY(-3px);
	}

	.task-text {
		flex: 1;
		color: #fff;
		font-size: 1rem;
	}

	.task-btns button {
		border: none;
		background-color: #ff4b5c;
		padding: 5px 10px;
		border-radius: 50%;
		color: #fff;
		cursor: pointer;
		transition: background-color 0.3s ease;
	}

	.task-btns button:hover {
		background-color: #ff2e3a;
	}
</style>