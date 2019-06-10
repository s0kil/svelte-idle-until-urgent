<script>
  export let url;
</script>

<style>
  img {
    max-height: 200px;
    margin-right: 2rem;
  }
</style>

<img on:load src={url} alt="" />
