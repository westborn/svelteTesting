<script lang="ts">
	import '../app.css'

	import { invalidate } from '$app/navigation'
	import { onMount } from 'svelte'
	import type { LayoutData } from './$types'

	export let data: LayoutData

	$: ({ supabase } = data)

	onMount(() => {
		const {
			data: { subscription },
		} = supabase.auth.onAuthStateChange(() => {
			invalidate('supabase:auth')
		})

		return () => subscription.unsubscribe()
	})
</script>

<svelte:head>
	<title>Event Registration</title>
</svelte:head>

<div class="container" style="padding: 50px 0 100px 0">
	<slot />
</div>
