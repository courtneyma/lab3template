<svelte:head>
  <title>Contact me</title>
</svelte:head>


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