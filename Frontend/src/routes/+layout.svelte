<script>
  import "../app.css";
</script>

<div class="mainlayout">
  <slot />
</div>

<style>
  :global(body) {
    background-color: transparent;
    background-image: url(../lib/images/background.jpg);
    background-size: cover;
    font-family: "Poppins", sans-serif;
  }
</style>
