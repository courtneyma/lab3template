<svelte:head>
  <title>Contact me</title>
</svelte:head>

<nav>
  <a href=".">Home</a>
  <a href="projects">Projects</a>
  <a class="current" href="contact">Contact</a>
  <a href="resume">Resume</a>
  <a href="https://github.com/courtneyma" target="_blank">GitHub</a>
</nav>

<h1>Contact</h1>

<form action="mailto:cma26@mit.edu" method="GET">
    <label>
        Email:
        <input type="email" name="email" />
    </label>

    <label>
        Subject:
        <input name="subject"  />
    </label>

    <label>
        Body:
        <textarea name="body"></textarea>
    </label>

    <button>Submit</button>
</form>