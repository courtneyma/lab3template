<script>
  import { base } from "$app/paths";
  import { page } from "$app/stores";

  let pages = [
    { url: "/", title: "Home" },
    { url: "/projects", title: "Projects" },
    { url: "/resume", title: "Resume" },
    { url: "/contact", title: "Contact" },
    { url: "https://github.com/courtneyma", title: "GitHub" }
  ];

  let localStorage = globalThis.localStorage ?? {};

  let colorScheme = "light dark";
  if (localStorage.colorScheme) {
    colorScheme = localStorage.colorScheme;
  }

  let root = globalThis.document?.documentElement;

  $: root?.style.setProperty("color-scheme", colorScheme);
  $: localStorage.colorScheme = colorScheme;
</script>

<label class="color-scheme-switch">
  Theme:
  <select bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
</label>

<nav>
  {#each pages as p}
    <a
      href={p.url.startsWith("http") ? p.url : base + p.url}
      class:current={p.url === "/"
        ? $page.url.pathname === (base + "/")
        : $page.url.pathname.startsWith(base + p.url)}
      target={p.url.startsWith("http") ? "_blank" : null}
    >
      {p.title}
    </a>
  {/each}
</nav>

<slot />

<style>
  .color-scheme-switch {
    position: absolute;
    top: 1rem;
    right: 1rem;
    display: inline-flex;
    gap: 4px;
    font-size: 80%;
    align-items: center;
  }

  nav {
    --border-color: oklch(50% 10% 200 / 40%);
    display: flex;
    margin-bottom: 1em;
    border-bottom: 2px solid var(--border-color);
  }

  nav a {
    flex: 1;
    text-decoration: none;
    color: inherit;
    text-align: center;
    padding: 0.5em;
    border-bottom: 0 solid transparent;
  }

  nav a.current {
    border-bottom-width: 0.4em;
    border-bottom-color: var(--border-color);
    padding-bottom: 0.1em;
  }

  nav a:hover {
    border-bottom-width: 0.4em;
    border-bottom-color: var(--color-accent);
    color: var(--color-accent);
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
  }
</style>