<script>
  export let data = {};
</script>

<article class="reading-item">
  <img src={data.image} alt="" />
  <div class="meta">
    <h3>{data.title}</h3>
    <p class="author">{data.author}</p>
  </div>
</article>

<style>
  .reading-item {
    display: flex;
    gap: 1em;
    align-items: center;
  }

  img {
    width: 80px;
    height: auto;
  }

  h3 {
    margin: 0;
    line-height: 1.1;
  }

  .author {
    margin: 0.25em 0 0;
    opacity: 0.8;
  }
</style>