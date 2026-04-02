<script>
  import { base } from "$app/paths";
  import { onMount } from "svelte";
  import * as d3 from "d3";

  import BarHorizontal from "$lib/BarHorizontal.svelte";

  import { computePosition, autoPlacement, offset } from "@floating-ui/dom";

  let locData = [];
  let commits = [];

  let width = 800;
  let height = 360;
  let margin = { top: 20, right: 20, bottom: 40, left: 60 };

  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let xAxis, yAxis, yAxisGridlines;

  let hoveredIndex = -1;
  $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};
  let commitTooltip;
  let tooltipPosition = { x: 0, y: 0 };

  let clickedCommits = [];

  let allTypes = []; 
  $: selectedLines =
    clickedCommits.length > 0 ? clickedCommits.flatMap((c) => c.lines) : locData;

  $: typeCounts = d3.rollup(selectedLines, (v) => v.length, (d) => d.type);

  $: barData =
    allTypes.length === 0
      ? []
      : allTypes.map((t) => ({
          label: t,
          value: typeCounts.get(t) ?? 0
        }));

  $: barTitle =
    clickedCommits.length > 0
      ? `Language breakdown for ${clickedCommits.length} selected commit(s)`
      : "Language breakdown for entire website";

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, (row) => ({
      ...row,
      line: Number(row.line),
      length: Number(row.length),
      depth: Number(row.depth),
      date: new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime)
    }));

    allTypes = Array.from(new Set(locData.map((d) => d.type))).sort();

    commits = d3.groups(locData, (d) => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let { author, date, time, timezone, datetime } = first;

      return {
        id: commit,
        url: "https://github.com/vis-society/lab-7/commit/" + commit,
        author,
        date,
        time,
        timezone,
        datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length,
        lines
      };
    });

    commits = d3.sort(commits, (d) => -d.totalLines);
  });

  $: minDate = d3.min(commits, (d) => d.datetime) ?? new Date();
  $: maxDateRaw = d3.max(commits, (d) => d.datetime) ?? new Date();
  $: maxDate = d3.timeDay.offset(maxDateRaw, 1); // +1 day

  $: xScale = d3
    .scaleTime()
    .domain([minDate, maxDate])
    .range([usableArea.left, usableArea.right])
    .nice();

  $: yScale = d3
    .scaleLinear()
    .domain([0, 24])
    .range([usableArea.bottom, usableArea.top]);

  $: linesExtent = d3.extent(commits, (d) => d.totalLines);
  $: rScale = d3
    .scaleSqrt()
    .domain(linesExtent[0] == null ? [1, 1] : linesExtent)
    .range([5, 30]);

  $: if (xAxis && yAxis && yAxisGridlines) {
    d3.select(xAxis).call(d3.axisBottom(xScale));

    d3.select(yAxis).call(
      d3.axisLeft(yScale).tickFormat(
        (d) => String(d % 24).padStart(2, "0") + ":00"
      )
    );

    d3.select(yAxisGridlines).call(
      d3.axisLeft(yScale).tickFormat("").tickSize(-usableArea.width)
    );
  }

  async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;

    if (evt.type === "mouseenter") {
      hoveredIndex = index;

      if (commitTooltip) {
        let pos = await computePosition(hoveredDot, commitTooltip, {
          strategy: "fixed",
          middleware: [offset(6), autoPlacement()]
        });
        tooltipPosition = pos;
      }
    } else if (evt.type === "mouseleave") {
      hoveredIndex = -1;
    } else if (evt.type === "click") {
      let commit = commits[index];
      if (!clickedCommits.includes(commit)) {
        clickedCommits = [...clickedCommits, commit];
      } else {
        clickedCommits = clickedCommits.filter((c) => c !== commit);
      }
    }
  }
</script>

<svelte:head>
  <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<p>Commits over time (x) vs time of day (y). Hover for details. Click to select commits.</p>

<svg viewBox="0 0 {width} {height}" class="scatter">
  <g
    class="gridlines"
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxisGridlines}
  />
  <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
  <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />

  <g class="dots">
    {#each commits as commit, index}
      <circle
        class:selected={clickedCommits.includes(commit)}
        cx={xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r={rScale(commit.totalLines)}
        on:mouseenter={(evt) => dotInteraction(index, evt)}
        on:mouseleave={(evt) => dotInteraction(index, evt)}
        on:click={(evt) => dotInteraction(index, evt)}
      />
    {/each}
  </g>
</svg>

<dl
  class="info tooltip"
  bind:this={commitTooltip}
  hidden={hoveredIndex === -1}
  style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px"
>
  <dt>Commit</dt>
  <dd>
    <a href={hoveredCommit.url} target="_blank">{hoveredCommit.id}</a>
  </dd>

  <dt>Author</dt>
  <dd>{hoveredCommit.author}</dd>

  <dt>Date</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", { dateStyle: "full" })}</dd>

  <dt>Time</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", { timeStyle: "short" })}</dd>

  <dt>Lines</dt>
  <dd>{hoveredCommit.totalLines}</dd>
</dl>

<BarHorizontal data={barData} title={barTitle} />

<style>
  svg.scatter {
    max-width: 100%;
    height: auto;
    overflow: visible;
  }

  .gridlines {
    stroke-opacity: 0.2;
  }

  circle {
    fill: steelblue;
    fill-opacity: 0.55;
    cursor: pointer;
    transition: 200ms;
  }

  circle:hover {
    fill: darkgreen;
    fill-opacity: 0.8;
  }

  .selected {
    fill: var(--color-accent);
    fill-opacity: 0.85;
  }

  dl.info {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 0.25rem 0.75rem;
    margin: 0;
  }

  dl.info dt {
    opacity: 0.7;
  }

  dl.info dd {
    margin: 0;
  }

  .tooltip {
    position: fixed;
    background: oklch(100% 0% 0 / 80%);
    backdrop-filter: blur(6px);
    padding: 0.75rem 0.9rem;
    border-radius: 10px;
    box-shadow: 0 10px 30px oklch(0% 0% 0 / 20%);
    transition-duration: 500ms;
    transition-property: opacity, visibility;
    max-width: min(420px, 90vw);
  }

  .tooltip[hidden]:not(:hover, :focus-within) {
    opacity: 0;
    visibility: hidden;
  }
</style>