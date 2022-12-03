<script lang="ts">
	import { applyAction, enhance, type SubmitFunction } from '$app/forms';
	import type { ActionData, PageData } from './$types';
	import toast from 'svelte-french-toast';
	import { invalidateAll } from '$app/navigation';

	export let data: PageData;
	export let form: ActionData;

	const submitCreateNote: SubmitFunction = ({ form, data, action, cancel }) => {
		const { title, content } = Object.fromEntries(data);

		if (title.length < 1) {
			toast.error('Title cannot be blank!');
			cancel();
		}

		return async ({ result, update }) => {
			switch (result.type) {
				case 'success':
					toast.success('Note added!');
					break;
				case 'invalid':
					toast.error('Title cannot be blank');
					break;
				default:
					break;
			}
			await update();
		};
	};

	const submitDeleteNote: SubmitFunction = () => {
		return async ({ result, update }) => {
			switch (result.type) {
				case 'success':
					toast.success('Deleted note!');
					break;
				default:
					break;
			}
			await update();
		};
	};
</script>

<div class="split">
	<div class="side">
		<form action="?/create" method="POST" class="form-create" use:enhance={submitCreateNote}>
			<label for="title"> Title </label>
			<input type="text" name="title" />
			<label for="content">Content</label>
			<input type="text" name="content" />
			<button type="submit">Add</button>
			{#if form?.errorMsg}
				<div class="alert error">{form.errorMsg}</div>
			{/if}
		</form>
	</div>
	<div class="side">
		{#each data.notes as note}
			<div class="note">
				<div>
					<h4>{note.title}</h4>
					<p>{note.content}</p>
				</div>
				<form action="?/delete" method="POST" use:enhance={submitDeleteNote}>
					<input type="hidden" name="title" value={note.title} />
					<button type="submit">❌</button>
				</form>
			</div>
		{/each}
	</div>
</div>

<style>
</style>
