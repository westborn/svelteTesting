<script lang="ts">
	import { onMount } from 'svelte'
	export let title: string | undefined = undefined
	export let src: string | Promise<string> | undefined = undefined
	export let placeholderSrc: string =
		'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR42mNkYAAAAAYAAjCB0C8AAAAASUVORK5CYII='
	let klass: string | undefined = undefined
	export { klass as class }

	let resolvedSrc: string = placeholderSrc
	let el: HTMLElement

	async function resolve(src: string | PromiseLike<string> | undefined) {
		try {
			resolvedSrc = src instanceof Promise ? await src : src
		} catch (e) {}
	}
	$: resolve(src)

	onMount(() => {
		const lazyBackgroundObserver = new IntersectionObserver(function (entries) {
			entries.forEach(function (entry) {
				if (entry.isIntersecting) {
					entry.target.classList.add('visible')
				} else {
					entry.target.classList.remove('visible')
				}
			})
		})
		lazyBackgroundObserver.observe(el)
		return () => {
			lazyBackgroundObserver.unobserve(el)
		}
	})
</script>

<div
	bind:this={el}
	class={`${klass}`}
	style={`--src: url(${resolvedSrc}); --placeholderSrc: url(${placeholderSrc});`}
	{title}
/>

<style lang="postcss">
	div {
		background-size: cover;
		background-repeat: no-repeat;
		background-image: var(--placeholderSrc);
	}
	div.visible {
		background-image: var(--src);
	}
</style>
