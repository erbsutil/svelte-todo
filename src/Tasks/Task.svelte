<script>
  let tasks = []; // array para armazenar dados de tarefas

  let newDescription = "";
  let validDescription = false;
  $: validDescription = newDescription.trim().length >= 5; // declaração reativa

  function newTask() {
    id = null;
    newDescription = "";
    document.getElementById("newDescriptionId").focus();
  }

  function submitTask() {
    insertTask(); // insere tarefas no banco de dados.
  }
  function insertTask() {
    const taskData = { description: newDescription };
    fetch("https://svelte-todo-app-93931-default-rtdb.firebaseio.com/tasks.json", {
      method: "POST",
      body: JSON.stringify(taskData),
      headers: { "Content-Type": "application/json" },
    })
      .then((response) => {
        if (!response.ok) {
          throw new error("An error occurred, please try again!");
        }
        return response.json();
      })
      .then((data) => {
        // Reatividade acionada por atribuição.
        tasks = [...tasks, { id: data.name, description: newDescription }];
        // data.name é como o Firebase armazena o ID do registro.
        newTask();
      })
      .catch((error) => console.log(error));
  }
</script>

<div class="container">
  <div class="notification">
    <!-- form -->
    <div class="field is-horizontal">
      <div class="field-label is-normal">
        <label class="label">Task</label>
      </div>
      <div class="field-body">
        <div class="field">
          <p class="control is-expanded has-icons-left">
            <input
              id="newDescriptionId"
              bind:value={newDescription}
              class="input"
              type="text"
              placeholder="Enter new task..."
            />
            <span class="icon is-small is-left">
              <i class="fas fa-tasks" />
            </span>
          </p>
        </div>
        <!-- Buttons -->
        <div class="field is-grouped">
          <div class="control">
            <button class="button is-normal" on:click={newTask}>New</button>
          </div>
          <div class="control">
            <button
              class="button is-info"
              on:click={submitTask}
              disabled={!validDescription}
            >
              <span class="icon is-small">
                <i class="fas fa-check" />
              </span>
              <span>Save</span>
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- Binding -->
    <div class="field is-horizontal adding-record">
      <div class="field-label">New record:</div>
      <div class="field-body">
        <div class="field">
          <div class="control">{newDescription}</div>
        </div>
      </div>
    </div>
  </div>
</div>
