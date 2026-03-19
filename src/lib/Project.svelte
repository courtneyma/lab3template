<script>
  import { base } from "$app/paths";
  export let data = {};
</script>

<article>
  <h2>{data.title}</h2>
  <p class="year">{data.year}</p>

  <img src={`${base}/${data.image}`} alt="" />
  <p>{data.description}</p>
</article>

<style>
  .year {
    margin: 0.25rem 0 0.5rem;
    font-size: 0.9em;
    opacity: 0.75;
  }

  img {
    max-width: 100%;
    height: auto;
    display: block;
  }
</style>