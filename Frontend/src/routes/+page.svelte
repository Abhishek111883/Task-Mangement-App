<script>
  // import Fetchtask from "./../component/Fetch_Task/fetchtask.svelte";
  import Addtask from "./../component/Add_Task/addtask.svelte";
  import { Button } from "flowbite-svelte";
  import { onMount } from "svelte";
  import { goto } from "$app/navigation";

  let loggedIn = false;

  const handleOnClick = () => {
    goto("/login");
  };

  function logout() {
    localStorage.removeItem("access_token");
    loggedIn = false;
  }

  onMount(() => {
    loggedIn = !!localStorage.getItem("access_token");
  });
</script>

<div>
  <div class="mt-20">
    <h1 class="font-bold text-center text-5xl">Hi, there!!</h1>
    <p class="font-bold text-center text-5xl text-primary-700">
      Welcome to Task Management
    </p>
  </div>

  <div class="text-center my-11">
    {#if loggedIn}
      <p class="font-semibold text-2xl mb-2">You are logged in..</p>
      <Button on:click={logout} class="font-bold text-1xl">Logout</Button>
    {:else}
      <p class="font-semibold text-2xl mb-2">Would you like to login..</p>
      <Button class="font-bold text-2xl px-5 mt-5" on:click={handleOnClick}>
        Login
      </Button>
    {/if}
  </div>

  {#if loggedIn}
    <Addtask access_token={localStorage.getItem("access_token")} />
  {/if}
</div>
