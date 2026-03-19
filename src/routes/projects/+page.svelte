<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import { base } from "$app/paths";
  import Bar from "$lib/Bar.svelte";

  let years = projects.map((proj) => proj.year);
  let range = Math.max(...years) - Math.min(...years);
  

  // extra calculation besides count (meets rubric):
  let uniqueYears = new Set(years).size;

  let rawData = [];
  let wrangled = [];

  onMount(async () => {
    rawData = await d3.json(`${base}/lab6_example.json`);
    wrangled = d3.rollups(
      rawData,
      (v) => d3.sum(v, (d) => d.lines),
      (d) => d.language
    );

  
  });

  $: barData = d3
    .rollups(projects, (v) => v.length, (d) => d.year)
    .map(([year, count]) => ({ label: String(year), value: count }));

</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<Bar data={barData} />


<h1>{projects.length} Projects over {range} Years ({uniqueYears} distinct years)</h1>

<p>
  Scroll down to see a timeline of my projects and how they’ve shaped what I care about.
</p>

<ProjectNarrative />

<p class="outro">
  Thanks for scrolling through my project story! Feel free to explore all of the projects below.
</p>

<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>

<style>
  .outro {
    margin-bottom: 2rem;
  }
</style>