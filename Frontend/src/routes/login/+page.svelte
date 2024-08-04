<script>
  import { createEventDispatcher } from "svelte";
  import axios from "axios";
  import { Card, Button, Label, Input, Checkbox } from "flowbite-svelte";
  import { Toast } from "flowbite-svelte";
  import { slide } from "svelte/transition";
  import { CheckCircleSolid } from "flowbite-svelte-icons";

  let toastStatus = false;
  let counter = 2;

  function trigger() {
    toastStatus = true;
    counter = 2;
    timeout();
  }

  function timeout() {
    if (--counter > 0) return setTimeout(timeout, 1000);
    toastStatus = false;
  }

  let email = "";
  let password = "";
  let errorMessage = "";
  const dispatch = createEventDispatcher();

  async function login() {
    try {
      const response = await axios.post("http://localhost:8055/auth/login", {
        email,
        password,
      });
      localStorage.setItem("access_token", response.data.data.access_token);
      dispatch("loginSuccess");
      trigger();
      // Redirect to home after successful login
      window.location.href = "/";
    } catch (error) {
      console.error("Login failed:", error);
      errorMessage = error.response
        ? error.response.data.message
        : "An error occurred";
    }
  }
</script>

<div class="backdrop-blur-sm bg-white/30">
  <div class="flex gap-10 items-center justify-center">
    <!-- <Button on:click={trigger} class="my-3">Restart</Button> -->
    <Toast dismissable={false} transition={slide} bind:toastStatus>
      <CheckCircleSolid slot="icon" class="w-5 h-5" />
      Login Successful
    </Toast>
  </div>

  <div class="flex items-center justify-center my-24 bg-transparent">
    <Card>
      <form class="flex flex-col space-y-6" on:submit|preventDefault={login}>
        <h3 class="text-xl font-medium text-gray-900 dark:text-white">
          Sign in to our platform
        </h3>
        <Label class="space-y-2">
          <span>Email</span>
          <Input
            bind:value={email}
            type="email"
            placeholder="name@gmail.com"
            required
          />
        </Label>
        <Label class="space-y-2">
          <span>Your password</span>
          <Input
            bind:value={password}
            type="password"
            placeholder="•••••"
            required
          />
        </Label>
        <div class="flex items-start">
          <Checkbox>Remember me</Checkbox>
          <a
            href="/"
            class="ms-auto text-sm text-primary-700 hover:underline dark:text-primary-500"
          >
            Lost password?
          </a>
        </div>
        <Button type="submit" class="w-full">Login to your account</Button>
        <div class="text-sm font-medium text-gray-500 dark:text-gray-300">
          Not registered? <a
            href="/"
            class="text-primary-700 hover:underline dark:text-primary-500"
          >
            Create account
          </a>
        </div>
      </form>
    </Card>
  </div>
</div>
