<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";

  import reading from "$lib/reading.json";
  import ReadingItem from "$lib/ReadingItem.svelte";
</script>

<nav>
  <a class="current" href=".">Home</a>
  <a href="projects">Projects</a>
  <a href="contact">Contact</a>
  <a href="resume">Resume</a>
  <a href="https://github.com/courtneyma" target="_blank">GitHub</a>
</nav>
<h1>Courtney</h1>
<p>hi! I'm Courtney a current senior here at MIT studying computer science and writing. I'm currently interested in spatial sound, podcasting, and videography, and I thought I'd never say this but I'm also a bit in my spiritual era. If you know anything about tinctures you should contact me!(lowkey not joking)</p>
<figure>
    <img src="images/rudiesbaby.JPG" alt="I WWOOFed on a farm over IAP and this is one of the baby cows born while I was there!" width="500" height="750">
    <figcaption>I WWOOFed on a farm over IAP and this is one of the baby cows born while I was there!</figcaption>
</figure>

<h2>Latest projects</h2>

<div class="projects">
  {#each projects.slice(0, 3) as p}
    <Project data={p} />
  {/each}
</div>

<h2>What I’m Reading</h2>

<div class="reading">
  {#each reading as item}
    <ReadingItem data={item} />
  {/each}
</div>
