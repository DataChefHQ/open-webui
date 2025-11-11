<script lang="ts">
	import { tick, getContext, onMount, onDestroy } from 'svelte';

	const i18n = getContext('i18n');

	export let command = '';
	export let onSelect = (e) => {};

	let selectedRoleIdx = 0;
	let filteredRoles = [];

	// Debug logging
	$: console.log('[Roles] command:', command, 'filteredRoles count:', filteredRoles.length);

	// Define available roles with descriptions
	const availableRoles = [
		{
			id: 'Architect',
			command: '/Architect',
			title: 'Data Architect',
			description: 'Design data models and interview users'
		},
		{
			id: 'Engineer',
			command: '/Engineer',
			title: 'Data Engineer',
			description: 'Generate SQL queries and transformations'
		}
	];

	$: filteredRoles = availableRoles
		.filter((r) => {
			const roleCmd = r.command.toLowerCase();
			const searchCmd = command.toLowerCase();
			// Show all roles if command is just '/'
			if (searchCmd === '/') return true;
			// Otherwise filter by what user is typing
			return roleCmd.startsWith(searchCmd);
		})
		.sort((a, b) => a.command.localeCompare(b.command));

	$: if (command) {
		selectedRoleIdx = 0;
	}

	export const selectUp = () => {
		selectedRoleIdx = Math.max(0, selectedRoleIdx - 1);
	};

	export const selectDown = () => {
		selectedRoleIdx = Math.min(selectedRoleIdx + 1, filteredRoles.length - 1);
	};

	let container;
	let adjustHeightDebounce;

	const adjustHeight = () => {
		if (container) {
			if (adjustHeightDebounce) {
				clearTimeout(adjustHeightDebounce);
			}

			adjustHeightDebounce = setTimeout(() => {
				if (!container) return;

				const rect = container.getBoundingClientRect();
				container.style.maxHeight = Math.max(Math.min(240, rect.bottom - 80), 100) + 'px';
			}, 100);
		}
	};

	const confirmRole = async (role) => {
		// Return the role ID to set the selected role
		onSelect({ type: 'role', data: role.id });
	};

	onMount(async () => {
		window.addEventListener('resize', adjustHeight);
		await tick();
		adjustHeight();
	});

	onDestroy(() => {
		window.removeEventListener('resize', adjustHeight);
	});
</script>

{#if filteredRoles.length > 0}
	<div
		id="commands-container"
		class="px-2 mb-2 text-left w-full absolute bottom-0 left-0 right-0 z-10"
	>
		<div class="flex w-full rounded-xl border border-gray-100 dark:border-gray-850">
			<div class="flex flex-col w-full rounded-xl bg-white dark:bg-gray-900 dark:text-gray-100">
				<div
					class="m-1 overflow-y-auto p-1 space-y-0.5 scrollbar-hidden max-h-60"
					id="command-options-container"
					bind:this={container}
				>
					{#each filteredRoles as roleItem, roleIdx}
						<button
							class="px-3 py-1.5 rounded-xl w-full text-left {roleIdx === selectedRoleIdx
								? 'bg-gray-50 dark:bg-gray-850 selected-command-option-button'
								: ''}"
							type="button"
							on:click={() => {
								confirmRole(roleItem);
							}}
							on:mousemove={() => {
								selectedRoleIdx = roleIdx;
							}}
							on:focus={() => {}}
						>
							<div class="flex items-center gap-2">
								<div class="font-medium text-purple-600 dark:text-purple-400">
									{roleItem.command}
								</div>
								<div class="text-xs text-gray-500 dark:text-gray-400">
									{roleItem.title}
								</div>
							</div>

							<div class="text-xs text-gray-600 dark:text-gray-100 mt-0.5">
								{roleItem.description}
							</div>
						</button>
					{/each}
				</div>

				<div
					class="px-2 pt-0.5 pb-1 text-xs text-gray-600 dark:text-gray-100 bg-white dark:bg-gray-900 rounded-b-xl flex items-center space-x-1"
				>
					<div>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 24 24"
							stroke-width="1.5"
							stroke="currentColor"
							class="w-3 h-3"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								d="m11.25 11.25.041-.02a.75.75 0 0 1 1.063.852l-.708 2.836a.75.75 0 0 0 1.063.853l.041-.021M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9-3.75h.008v.008H12V8.25Z"
							/>
						</svg>
					</div>

					<div class="line-clamp-1">
						{$i18n.t(
							'Tip: Select a role to prefix your message. The role will be sent to n8n for persona-driven responses.'
						)}
					</div>
				</div>
			</div>
		</div>
	</div>
{/if}
