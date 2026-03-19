<script>
  import * as d3 from "d3";

  export let data = [];

  let width = 560;
  let height = 320;

  let margin = { top: 40, right: 150, bottom: 60, left: 90 };
  $: innerWidth = width - margin.left - margin.right;
  $: innerHeight = height - margin.top - margin.bottom;

  $: xMax = d3.max(data, (d) => d.value) || 1;

  $: xScale = d3.scaleLinear().domain([0, xMax]).range([0, innerWidth]);

  $: yScale = d3
    .scaleBand()
    .domain(data.map((d) => d.label))
    .range([0, innerHeight])
    .padding(0.2);

  $: colorScale = d3.scaleOrdinal(d3.schemeTableau10).domain(data.map((d) => d.label));

  let xAxis, yAxis;

  $: if (xAxis && yAxis) {
    d3.select(xAxis).call(d3.axisBottom(xScale));
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
      Lines of Code by Language
    </text>

    <!-- axes -->
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

      <!-- axis labels -->
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

      <!-- annotation -->
      {#if maxBar}
        <rect
          x={0}
          y={yScale(maxBar.label)}
          width={xScale(maxBar.value)}
          height={yScale.bandwidth()}
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        />

        <line
          x1={xScale(maxBar.value)}
          y1={yScale(maxBar.label) + yScale.bandwidth() / 2}
          x2={xScale(maxBar.value) + 30}
          y2={yScale(maxBar.label) + yScale.bandwidth() / 2}
          stroke="currentColor"
          stroke-width="1"
        />

        <text
          x={xScale(maxBar.value) + 35}
          y={yScale(maxBar.label) + yScale.bandwidth() / 2}
          dominant-baseline="middle"
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
    gap: 0.5rem;
  }

  .legend li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .swatch {
    width: 12px;
    height: 12px;
    background: var(--color);
    display: inline-block;
  }

  .chart-title {
    font-size: 1em;
    font-weight: bold;
    fill: currentColor;
  }

  .axis-label {
    font-size: 0.8em;
    fill: currentColor;
  }

  .annotation {
    font-size: 0.7em;
    fill: currentColor;
    font-style: italic;
  }
</style>