<script lang='ts'>
    import { Device } from '$lib/common/classes'
	export let device = new Device()
</script>

<div class="mb-1 mr-1 btn btn-xs btn-primary normal-case" tabindex="0">{device.user.name}</div>