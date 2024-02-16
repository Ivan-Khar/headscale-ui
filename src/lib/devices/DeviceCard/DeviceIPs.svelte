<script lang='ts'>
	import { Device } from '$lib/common/classes';
	export let device = new Device();

	function goToIP(ip: string) {
		if(isIPv4(ip)) {
			window.open('https://' + ip + '')
		} else if(isIPv6(ip)) { 
			window.open('https://[' + ip + ']')
		}
	}

	function isIPv4(ip: string) {
		var regex = /^(?!0)(?!.*\.$)((1?\d?\d|25[0-5]|2[0-4]\d)(\.|$)){4}$/
		return regex.test(ip)
	}

	function isIPv6(ip: string) {
		var regex = /(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:){1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4}){1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9]))/
		return regex.test(ip)
	}
	//<div class="mb-1 mr-1 normal-case">{device.machineKey}</div>
</script>

{#each device.ipAddresses as ip}
	<button class="mb-1 mr-1 btn btn-xs btn-primary normal-case" tabindex="0" on:click|stopPropagation={() => goToIP(ip)}>Открыть панель</button>
{/each}

