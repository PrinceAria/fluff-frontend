<script lang="ts">
  import {Router, Route, Link} from 'svelte-routing';
  import ToDoList from "./ToDoList.svelte";
  import Home from "./Home.svelte";
  import APIFetch from "./APIFetch.svelte";
  import Wordle from "./Wordle.svelte";
</script>

<Router>
  <nav>
    <Link class="link" to="/home">Home</Link>
    <br>
    <Link class="link" to="/todos">ToDoList</Link>
    <br>
    <Link class="link" to="/api">APIFetch</Link>
    <br>
    <Link class="link" to="/wordle">Wordle</Link>
  </nav>
  <div>
    <main>
      <Route path="/home" component={Home} />
      <Route path="/todos" component={ToDoList} />
      <Route path="/api" component={APIFetch} />
      <Route path="/wordle" component={Wordle} />
    </main>
  </div>
</Router>


<link type="text/css" rel="stylesheet" href="app.css">
