<script lang="ts">
	import { WEBUI_BASE_URL } from '$lib/constants';

	export let className = 'size-8';
	export let src = `${WEBUI_BASE_URL}/DataChef_Logo.svg`;
</script>

<img
	crossorigin="anonymous"
	aria-hidden="true"
	src={src === ''
		? `${WEBUI_BASE_URL}/DataChef_Logo.svg`
		: src.startsWith(WEBUI_BASE_URL) ||
			  src.startsWith('https://www.gravatar.com/avatar/') ||
			  src.startsWith('data:') ||
			  src.startsWith('/')
			? src
			: `${WEBUI_BASE_URL}/DataChef_Logo.svg`}
	class=" {className} object-cover rounded-full"
	alt="profile"
	draggable="false"
/>
