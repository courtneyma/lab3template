<script>
  import projects from "$lib/projects.json";
</script>

<svelte:head>

  <title>Projects</title>
</svelte:head>


<nav>
  <a href=".">Home</a>
  <a class="current" href="projects">Projects</a>
  <a href="contact">Contact</a>
  <a href="resume">Resume</a>
  <a href="https://github.com/courtneyma" target="_blank">GitHub</a>
</nav>
<h1>Projects</h1>
<div class="projects">
  {#each projects as p}
    <article>
      <h2>Lorem ipsum dolor sit.</h2>
      <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="" />
      <p>
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Magnam dolor quos,
        quod assumenda explicabo odio, nobis ipsa laudantium quas eum veritatis
        ullam sint porro minima modi molestias doloribus cumque odit.
      </p>
    </article>
  {/each}
</div>