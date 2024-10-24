<script>	
	import Main from './main/main.svelte';
  	import Admin from './admin/admin.svelte';
  	
  	import Router from 'svelte-spa-router';
  
  const routes = {
    '/': Main,
    '/admin': Admin,    
  };
	
</script>

<main>	  
	  <Router {routes} />
</main>

<style>
	main {
		text-align: center;
		padding: 1em;		
		margin: 0 auto;
	}
</style>
