<script>
  import Router from "svelte-spa-router";
  import { wrap } from "svelte-spa-router/wrap";
  import { push } from "svelte-spa-router";

  import Users from "./Users.svelte";
  import Categories from "./Categories.svelte";
  import Events from "./Events.svelte";

  let isAdmin = true;

  const toggleIsAdmin = () => {
    isAdmin = !isAdmin;
    push("/");
  };

  const routes = {
    "/users": wrap({
      component: Users,
      props: { foo: "bar" },
      conditions: [
        (detail) => {
          console.log(detail);
          return isAdmin;
        },
      ],
    }),
    "/categories": wrap({
      component: Categories,
      props: { foo: "bar" },
    }),
    "/events": wrap({
      component: Events,
      props: { foo: "bar" },
    }),
  };
</script>

<ul>
  <li>
    <a href="/">Home</a>
    <a href="#/users">Users</a>
    <a href="#/categories">Categories</a>
    <a href="#/events">Events</a>
  </li>
</ul>
<button on:click={toggleIsAdmin}>toggle is admin</button>
<div>{isAdmin}</div>

<Router {routes} />

<style>
</style>
