<script>
  import * as d3 from "d3";

  export let data = [];
  export let title = "";

  let width = 720;
  let height = 260;

  let margin = { top: 35, right: 110, bottom: 55, left: 90 };
  $: innerWidth = width - margin.left - margin.right;
  $: innerHeight = height - margin.top - margin.bottom;

  $: xMax = d3.max(data, (d) => d.value) || 1;

  $: xScale = d3.scaleLinear().domain([0, xMax]).range([0, innerWidth]).nice();

  $: yScale = d3
    .scaleBand()
    .domain(data.map((d) => d.label))
    .range([0, innerHeight])
    .padding(0.2);

  $: colorScale = d3.scaleOrdinal(d3.schemeTableau10).domain(data.map((d) => d.label));

  let xAxis, yAxis;

  $: if (xAxis && yAxis) {
    d3.select(xAxis).call(
      d3
        .axisBottom(xScale)
        .ticks(Math.min(10, xMax))
        .tickFormat((d) => (Number.isInteger(d) ? d : ""))
    );
    d3.select(yAxis).call(d3.axisLeft(yScale));
  }

  $: maxBar = d3.greatest(data, (d) => d.value);
</script>

<div class="container">
  <svg viewBox="0 0 {width} {height}">
    <text
      x={margin.left + innerWidth / 2}
      y={margin.top / 2}
      text-anchor="middle"
      class="chart-title"
    >
      {title}
    </text>

    <g transform="translate({margin.left}, {margin.top + innerHeight})" bind:this={xAxis} />
    <g transform="translate({margin.left}, {margin.top})" bind:this={yAxis} />

    <g transform="translate({margin.left}, {margin.top})">
      {#each data as d}
        <rect
          x={0}
          y={yScale(d.label)}
          width={xScale(d.value)}
          height={yScale.bandwidth()}
          fill={colorScale(d.label)}
        />
      {/each}

      <text x={innerWidth / 2} y={innerHeight + margin.bottom + 10} text-anchor="middle" class="axis-label">
        Lines of Code
      </text>

      <text
        x={-(innerHeight / 2)}
        y={-margin.left + 20}
        text-anchor="middle"
        transform="rotate(-90)"
        class="axis-label"
      >
        Language
      </text>

      {#if maxBar}
        <text
          x={Math.min(innerWidth - 5, xScale(maxBar.value) + 8)}
          y={yScale(maxBar.label) + yScale.bandwidth() / 2}
          dominant-baseline="middle"
          text-anchor="start"
          class="annotation"
        >
          Most lines
        </text>
      {/if}
    </g>
  </svg>

  <ul class="legend">
    {#each data as d}
      <li style="--color: {colorScale(d.label)}">
        <span class="swatch"></span>
        {d.label} <em>({d.value})</em>
      </li>
    {/each}
  </ul>
</div>

<style>
  svg {
    max-width: 100%;
    height: auto;
    overflow: visible;
  }

  .container {
    display: flex;
    gap: 1rem;
    align-items: flex-start;
  }

  .legend {
    list-style: none;
    padding: 0;
    margin: 0;
    flex: 1;
    display: grid;
    gap: 0.4rem;
  }

  .legend li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.9em;
  }

  .swatch {
    width: 12px;
    height: 12px;
    background: var(--color);
    display: inline-block;
  }

  .chart-title {
    font-size: 0.95em;
    font-weight: 700;
    fill: currentColor;
  }

  .axis-label {
    font-size: 0.75em;
    fill: currentColor;
  }

  .annotation {
    font-size: 0.7em;
    fill: currentColor;
    font-style: italic;
  }
</style>