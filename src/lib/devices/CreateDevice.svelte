<script lang="ts">
	import { fade } from 'svelte/transition';
	import { userStore } from '$lib/common/stores';
	import { getDevices, newDevice } from '$lib/common/apiFunctions.svelte';
	import { alertStore } from '$lib/common/stores.js';
	import { base } from '$app/paths';
	import { page } from '$app/stores';
	import { goto } from "$app/navigation";

	// whether the new card html element is visible
	export let newDeviceCardVisible = false;
	export let newDeviceKey = '';
	let newDeviceForm: HTMLFormElement;
	let selectedUser = '';

	let tabs = ['Стандартная установка', 'С помощью ключа связывания устройства'];
	let activeTab = 0;

	function newDeviceAction() {
		if (newDeviceForm.reportValidity()) {
			newDevice(newDeviceKey, selectedUser)
				.then((response) => {
					newDeviceCardVisible = false;
					newDeviceKey = '';

					// refresh devices after editing
					getDevices();

					// Clear device key in url
					if ($page.url.searchParams.get('nodekey')) {
						$page.url.searchParams.delete('nodekey');
						goto(`?${$page.url.searchParams.toString()}`);
					}
				})
				.catch((error) => {
					$alertStore = error;
				});
		} else {
			$alertStore = 'ключ не подходит';
		}
	}
</script>

<!-- html -->

{#if newDeviceCardVisible == true}
	<div in:fade|global out:fade|global={{ duration: newDeviceCardVisible ? 0 : 500 }} class="p-2 max-w-screen-lg border border-dashed border-base-content mx-4 rounded-md text-sm text-base-content shadow mb-10">
		<div class="tabs">
			{#each tabs as tab, index}
				<button class="tab tab-bordered h-fit w-1/2" class:tab-active={activeTab == index} on:click={() => (activeTab = index)}>{tab}</button>
			{/each}
		</div>
		<!-- Default Configuration -->
		{#if activeTab == 0}
			<div in:fade|global class="m-2">
				<p> Установите Tailscale и настройте его чтобы он подсоединялся к серверу HeadScale (как это сделать: <a target="_blank" rel="noreferrer" class="link link-primary" href="https://headscale.net/windows-client/">ссылка</a>). При нажатии иконки Tailscale в трее должно открытся окно с данными для входа по типу:</p>
				<div class="m-2"><code>headscale -u USER nodes register --key &lt;ключ устройства&gt;</code></div>
				<div class="my-2"><p>Вставьте ключ устройства сюда (не забудьте создать пользователя для устройства во вкладке <a href="{base}/users.html" class="link link-primary">Пользователи</a>):</p></div>
				<form class="flex flex-wrap" bind:this={newDeviceForm} on:submit|preventDefault={newDeviceAction}>
					<div class="flex-none mr-4">
						<label class="block text-secondary text-sm font-bold mb-2" for="text">Ключ устройства</label>
						<input bind:value={newDeviceKey} minlength="54" class="card-input" type="text" required placeholder="nodekey:******************" />
					</div>
					<div class="flex-none">
						<label class="block text-secondary text-sm font-bold mb-2" for="select">Select User</label>
						<select class="card-select mr-3" required bind:value={selectedUser}>
							{#each $userStore as user}
								<option>{user.name}</option>
							{/each}
						</select>
					</div>
					<div class="flex-none pt-6">
						<button
							><svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 mx-1 inline rounded-full hover:bg-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
								<path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
							</svg></button
						>
						<button
							on:click={() => {
								newDeviceCardVisible = false;
								newDeviceKey = '';
							}}
							type="button"
							><svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 mx-1 inline rounded-full hover:bg-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
								<path stroke-linecap="round" stroke-linejoin="round" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" />
							</svg></button
						>
					</div>
				</form>
			</div>
		{/if}
		<!-- With Preauth Keys -->
		{#if activeTab == 1}
			<div in:fade|global class="m-2">
				<p>Для входа с помощью ключа связывания устройств нужно запустить tailscale с флагом --authkey, не забудьте указать ссылку на сам сервер используя флаг --login-server.</p>
				<div class="m-2"><code>tailscale login --force-reauth --login-server=&lt;ссылка на сервер&gt; --authkey=&lt;ключ связывания&gt;</code></div>
				<div class="bg-base-200 p-4 m-2 rounded-xl">
					<svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 inline flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
						<path stroke-linecap="round" stroke-linejoin="round" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
					</svg>
					<span class="pl-2">Управлять ключами можно во вкладке <a href="{base}/users.html" class="link link-primary">Пользователи</a></span>
				</div>
			</div>
		{/if}
	</div>
{/if}
