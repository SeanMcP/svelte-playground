<script>
  let task = "";
  let tasks = JSON.parse(localStorage.tasks || "[]");

  $: {
    console.log("Storing tasks")
    localStorage.setItem("tasks", JSON.stringify(tasks));
  }

  function handleSubmit(e) {
    e.preventDefault();
    tasks = [
      ...tasks,
      {
        text: task,
        id: Date.now(),
        complete: false,
      },
    ];
    task = "";
  }

  function handleChange(e) {
    tasks = tasks.map((t) => {
      if (t.id === e.target.dataset.id) {
        t.complete = !t.complete;
      }
      return t;
    });
  }

  let orderedTasks = []
  $: {
    const sorted = tasks.sort((a, b) => a.id - b.id)
    const incomplete = []
    const complete = []
    sorted.forEach((t) => {
      if (t.complete) {
        complete.push(t)
      } else {
        incomplete.push(t)
      }
    })
    orderedTasks = [...incomplete, ...complete]
    orderedTasks = tasks.sort((a, b) => {
      if (a.complete && !b.complete) {
        return 1;
      }
      if (!a.complete && b.complete) {
        return -1;
      }
      return 0;
    });
  }
</script>

<main>
  <form on:submit={handleSubmit}>
    <input aria-label="Task" bind:value={task} />
    <button>Add</button>
  </form>
  <ul>
    {#each orderedTasks as task}
      <li>
        <input
          bind:checked={task.complete}
          on:change={handleChange}
          data-id={task.id}
          type="checkbox"
        />
        <span>{task.text}</span>
      </li>
    {/each}
  </ul>
</main>

<style>
</style>
