<script>
	export let title = '';
	let isHovered = false;
	let x = 0;
	let y = 0;

	function mouseOver(event) {
		isHovered = true;
		updatePosition(event);
	}
	function mouseMove(event) {
		updatePosition(event);
	}
	function mouseLeave() {
		isHovered = false;
	}
	
	function updatePosition(event) {
		x = event.pageX + 5;
		y = event.pageY + 5;
	}
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-mouse-events-have-key-events -->
<div
	on:mouseover={mouseOver}
	on:mouseleave={mouseLeave}
	on:mousemove={mouseMove}>
	<slot />
</div>

{#if isHovered}
	<div style="top: {y}px; left: {x}px;" class="tooltip">{title}</div>
{/if}

<style>
	.tooltip {
		border: 1px solid #ddd;
		box-shadow: 1px 1px 1px #ddd;
		background: white;
		border-radius: 4px;
		padding: 4px;
		position: absolute;
		pointer-events: none; /* 툴팁 자체가 마우스 이벤트에 반응하지 않도록 설정 */
		transition: transform 0.1s ease; /* 부드러운 이동을 위해 추가 */
		transform: translate(5px, 5px); /* 살짝 이동 */
	}
</style>
