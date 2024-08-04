<script>
  export let access_token;
  import { Button } from "flowbite-svelte";
  import { Toast } from "flowbite-svelte";
  import { blur, slide } from "svelte/transition";
  import { BellOutline, CheckCircleSolid } from "flowbite-svelte-icons";
  import { onMount } from "svelte";

  let toastStatusAdd = false;
  let toastStatusRemove = false;

  let Title = "";
  let Description = "";
  let Update_Status = "";
  let Due_Date = "";
  let error = "";
  let tasks = [];

  function validateForm() {
    if (
      Title === "" ||
      Description === "" ||
      Update_Status === "" ||
      Due_Date === ""
    ) {
      error = "Please fill in all fields";
      return false;
    }
    return true;
  }

  function triggerAddToast() {
    toastStatusAdd = true;
    setTimeout(() => {
      toastStatusAdd = false;
    }, 2000); // Hide toast after 2 seconds
  }

  function triggerRemoveToast() {
    toastStatusRemove = true;
    setTimeout(() => {
      toastStatusRemove = false;
    }, 2000); // Hide toast after 2 seconds
  }

  async function addTask() {
    if (!validateForm()) {
      return;
    }

    try {
      const response = await fetch("http://localhost:8055/items/tasks", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${access_token}`,
        },
        body: JSON.stringify({ Title, Description, Update_Status, Due_Date }),
      });

      if (!response.ok) {
        const errorData = await response.json();
        error = errorData.message || "An error occurred";
        return;
      }

      const data = await response.json();
      tasks = [data.data, ...tasks];
      triggerAddToast();
      clearForm();
    } catch (err) {
      error = "An error occurred while adding the task";
    }
  }

  function clearForm() {
    Title = "";
    Description = "";
    Update_Status = "";
    Due_Date = "";
    error = "";
  }

  async function fetchTasks() {
    try {
      const response = await fetch("http://localhost:8055/items/tasks", {
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${access_token}`,
        },
      });

      if (!response.ok) {
        const errorData = await response.json();
        error = errorData.message || "An error occurred";
        return;
      }

      const data = await response.json();
      tasks = data.data.reverse();
    } catch (err) {
      error = "An error occurred while fetching tasks";
    }
  }

  async function deleteTask(taskId) {
    try {
      const response = await fetch(
        `http://localhost:8055/items/tasks/${taskId}`,
        {
          method: "DELETE",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${access_token}`,
          },
        }
      );

      if (!response.ok) {
        const errorData = await response.json();
        error = errorData.message || "An error occurred";
        return;
      }

      tasks = tasks.filter((task) => task.id !== taskId); // Remove the task from the array
      triggerRemoveToast();
    } catch (err) {
      error = "An error occurred while deleting the task";
    }
  }

  onMount(fetchTasks);
</script>

<div>
  {#if error}
    <div class="fixed top-2 left-[42%] z-50">
      <Toast transition={blur} color="red" params={{ amount: 50, delay: 1000 }}>
        <BellOutline slot="icon" class="w-6 h-6" />
        {error}
      </Toast>
    </div>
  {/if}

  {#if toastStatusAdd}
    <div class="fixed top-2 left-[42%] z-50">
      <Toast dismissable={false} transition={slide} bind:toastStatusAdd>
        <CheckCircleSolid slot="icon" class="w-5 h-5" />
        Task added
      </Toast>
    </div>
  {/if}

  {#if toastStatusRemove}
    <div class="fixed top-2 left-[42%] z-50">
      <Toast dismissable={false} transition={slide} bind:toastStatusRemove>
        <CheckCircleSolid slot="icon" class="w-5 h-5" />
        Task removed
      </Toast>
    </div>
  {/if}

  <div class="w-[90%] mx-auto">
    <form
      on:submit|preventDefault={addTask}
      class="md:flex justify-between items-center gap-4 flex-wrap mt-8"
    >
      <div class="flex flex-col md:w-[350px] font-bold text-xl mb-6 md:mb-0">
        <label for="task">Task</label>
        <input
          bind:value={Title}
          type="text"
          placeholder="Enter Title"
          name="task"
          id="task"
          class="form-control rounded-xl border-2 hover:border-orange-500
           focus:outline-none focus:ring-0 focus:border-orange-500 p-4 placeholder:text-base"
        />
      </div>
      <div
        class="form-group flex flex-col flex-1 font-bold text-xl mb-6 md:mb-0"
      >
        <label for="description">Description</label>
        <input
          bind:value={Description}
          type="text"
          name="description"
          id="description"
          class="form-control rounded-xl border-2 hover:border-orange-500
           focus:outline-none focus:ring-0 focus:border-orange-500 p-4 placeholder:text-base"
          placeholder="Enter your Description"
        />
      </div>

      <div class="form-group flex flex-col font-bold text-xl mb-6 md:mb-0">
        <label for="Update_Status">Status</label>
        <select
          bind:value={Update_Status}
          name="Update_Status"
          id="Status"
          placeholder="Select Update_Status"
          class="form-control rounded-xl border-2 hover:border-orange-500
           focus:outline-none focus:ring-0 focus:border-orange-500 p-4 placeholder:text-base"
        >
          <option value="TO-DO">TO-DO</option>
          <option value="Completed">Completed</option>
        </select>
      </div>

      <div class="form-group flex flex-col font-bold text-xl mb-6 md:mb-0">
        <label for="due_date">Due Date</label>
        <input
          bind:value={Due_Date}
          type="datetime-local"
          name="due_date"
          id="due_date"
          placeholder="Due Date"
          class="form-control rounded-xl border-2 hover:border-orange-500
           focus:outline-none focus:ring-0 focus:border-orange-500 p-4 placeholder:text-base"
        />
      </div>

      <Button type="submit" class="btn btn-primary mt-4 md:mt-7 text-lg"
        >Add Task</Button
      >
    </form>
  </div>

  <div>
    {#each tasks as task}
      <div class="md:flex justify-between w-[90%] mx-auto my-10">
        <div class="gap-2 py-3 shadow-xl rounded-xl">
          <div
            class="gap-4 md:flex items-center justify-center text-base font-normal flex-wrap"
          >
            <div
              class="md:w-[350px] md:pl-2 text-center md:text-start mb-4 md:mb-0"
            >
              {task.Title}
            </div>
            <div
              class="md:w-[470px] md:pl-2 text-center md:text-start mb-4 md:mb-0"
            >
              {task.Description}
            </div>
            {#if task.Update_Status === "TO-DO"}
              <div
                class="md:w-40 text-center md:text-start text-red-600 mb-4 md:mb-0"
              >
                {task.Update_Status}
              </div>
            {:else}
              <div
                class="md:w-40 text-center md:text-start text-green-700 mb-4 md:mb-0"
              >
                {task.Update_Status}
              </div>
            {/if}

            <div class="md:w-52 text-center md:text-start mb-4 md:mb-0">
              {task.Due_Date}
            </div>

            <Button
              on:click={() => deleteTask(task.id)}
              class="btn btn-primary text-xl bg-red-700 text-center"
              >Delete</Button
            >
          </div>
        </div>
      </div>
    {/each}
  </div>
</div>
