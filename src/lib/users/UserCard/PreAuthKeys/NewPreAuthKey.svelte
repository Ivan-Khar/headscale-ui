<script>
	import { newPreAuthKey, getPreauthKeys } from '$lib/common/apiFunctions.svelte';
	import { PreAuthKey, User } from '$lib/common/classes';
	import { fade } from 'svelte/transition';
  import { alertStore } from '$lib/common/stores';
	let currentTime = new Date();
	let minDate = new Date(currentTime.setMinutes(currentTime.getMinutes() + 60 - currentTime.getTimezoneOffset())).toISOString().slice(0, 16);

	export let newPreAuthKeyShow = false;
	export let user = new User();
  export let keyList = [new PreAuthKey];
	let expiry = minDate;
	let reusable = false;
	let ephemeral = false;

	function NewPreAuthKeyAction() {
    let formattedDate = new Date(expiry).toISOString();
		newPreAuthKey(user.name, formattedDate, reusable, ephemeral)
			.then(() => {
				newPreAuthKeyShow = false;
				getPreauthKeysAction();
			})
			.catch((error) => {
				$alertStore = error;
			});
	}

  function getPreauthKeysAction() {
		getPreauthKeys(user.name)
			.then((keys) => {
				keyList = keys;
			})
			.catch((error) => {
				$alertStore = error;
			});
	}
</script>

<div in:fade|global class="card-pending">
	<form on:submit|preventDefault={NewPreAuthKeyAction}>
		<table class="table table-compact w-full">
			<tbody>
				<tr>
					<th>Срок окончания:</th>
					<td><input bind:value={expiry} class="border rounded px-2" type="datetime-local" required min={minDate} /><br /></td>
				</tr>
				<tr>
					<th>Многоразовый:</th>
					<td>
						<input type="checkbox" bind:checked={reusable} class="checkbox checkbox-sm text-base-content" />
					</td>
				</tr>
				<tr>
					<th>Эфемерный:</th>
					<td>
						<input type="checkbox" bind:checked={ephemeral} class="checkbox checkbox-sm text-base-content" />
					</td>
				</tr>
			</tbody>
		</table>
		<button class="btn btn-sm m-3 btn-primary capitalize">Создать ключ</button>
		<button
			on:click={() => {
				newPreAuthKeyShow = false;
			}}
			type="button"
			class="btn btn-sm m-1 btn-secondary capitalize">Отменить</button
		>
	</form>
</div>
