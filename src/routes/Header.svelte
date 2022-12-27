<script>

	import PocketBase from 'pocketbase';
	const pb = new PocketBase('http://170.187.151.26:8080');


	const logout = () => {
		pb.authStore.clear();
	}

</script>

<div class="navbar bg-base-100">
	<div class="flex-1">
	  <a class="btn btn-ghost normal-case text-xl">Chat App</a>
	</div>
	<div class="flex-none">
	  <ul class="menu menu-horizontal px-1">
		<li><a href="/login">Login or Signup</a></li>
		<li><a href="/feed">Chat</a></li>
	  </ul>
	</div>
	<button on:click={logout} class="btn btn primary">Logout</button>
  </div>
