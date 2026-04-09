<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	const USER_ID = '760941859669016647';
	let status = $state('offline');
	let ws: WebSocket | undefined;
	let heartbeat: ReturnType<typeof setInterval> | undefined;

	onMount(() => {
		ws = new WebSocket('wss://api.lanyard.rest/socket');

		ws.onopen = () => {
			ws?.send(
				JSON.stringify({
					op: 2,
					d: { subscribe_to_id: USER_ID }
				})
			);

			heartbeat = setInterval(() => {
				ws?.send(JSON.stringify({ op: 3 }));
			}, 30000);
		};

		ws.onmessage = (event) => {
			const msg = JSON.parse(event.data);
			if (msg.t === 'INIT_STATE' || msg.t === 'PRESENCE_UPDATE') {
				status = msg.d.discord_status ?? 'offline';
			}
		};

		ws.onerror = () => (status = 'offline');
		ws.onclose = () => (status = 'offline');
	});

	onDestroy(() => {
		clearInterval(heartbeat);
		ws?.close();
	});

	function formatStatus(s: string) {
		return s === 'dnd' ? 'Do Not Disturb' : s.charAt(0).toUpperCase() + s.slice(1);
	}

	function getStatusMessage(s: string) {
		return s === 'dnd' ? `I'm on ${formatStatus(s)}` : `I'm ${formatStatus(s)}`;
	}
</script>

<div class="pill">
	<svg role="img" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"
		><title>Discord</title><path
			d="M20.317 4.3698a19.7913 19.7913 0 00-4.8851-1.5152.0741.0741 0 00-.0785.0371c-.211.3753-.4447.8648-.6083 1.2495-1.8447-.2762-3.68-.2762-5.4868 0-.1636-.3933-.4058-.8742-.6177-1.2495a.077.077 0 00-.0785-.037 19.7363 19.7363 0 00-4.8852 1.515.0699.0699 0 00-.0321.0277C.5334 9.0458-.319 13.5799.0992 18.0578a.0824.0824 0 00.0312.0561c2.0528 1.5076 4.0413 2.4228 5.9929 3.0294a.0777.0777 0 00.0842-.0276c.4616-.6304.8731-1.2952 1.226-1.9942a.076.076 0 00-.0416-.1057c-.6528-.2476-1.2743-.5495-1.8722-.8923a.077.077 0 01-.0076-.1277c.1258-.0943.2517-.1923.3718-.2914a.0743.0743 0 01.0776-.0105c3.9278 1.7933 8.18 1.7933 12.0614 0a.0739.0739 0 01.0785.0095c.1202.099.246.1981.3728.2924a.077.077 0 01-.0066.1276 12.2986 12.2986 0 01-1.873.8914.0766.0766 0 00-.0407.1067c.3604.698.7719 1.3628 1.225 1.9932a.076.076 0 00.0842.0286c1.961-.6067 3.9495-1.5219 6.0023-3.0294a.077.077 0 00.0313-.0552c.5004-5.177-.8382-9.6739-3.5485-13.6604a.061.061 0 00-.0312-.0286zM8.02 15.3312c-1.1825 0-2.1569-1.0857-2.1569-2.419 0-1.3332.9555-2.4189 2.157-2.4189 1.2108 0 2.1757 1.0952 2.1568 2.419 0 1.3332-.9555 2.4189-2.1569 2.4189zm7.9748 0c-1.1825 0-2.1569-1.0857-2.1569-2.419 0-1.3332.9554-2.4189 2.1569-2.4189 1.2108 0 2.1757 1.0952 2.1568 2.419 0 1.3332-.946 2.4189-2.1568 2.4189Z"
		/></svg
	>

	<span>{getStatusMessage(status)}</span>

	<span class={`dot ${status}`}></span>
</div>

<style>
	.pill {
		display: inline-flex;
		align-items: center;
		gap: 0.5em;

		padding: 0.6em 1.1em;
		border-radius: var(--radius);

		background: var(--pill-background-color);
		color: var(--pill-text-color);

		font-size: 16px;
		line-height: 1;
		margin: 10px 0;
	}

	.pill svg {
		width: 1.1em;
		height: 1.1em;
		flex-shrink: 0;
		fill: var(--icon-color);
	}

	.pill span {
		line-height: 1.2;
	}

	.dot {
		width: 0.6em;
		height: 0.6em;
		border-radius: 50%;
		position: relative;
	}

	.dot.online {
		background: var(--discord-status-online);
	}
	.dot.idle {
		background: var(--discord-status-idle);
	}
	.dot.dnd {
		background: var(--discord-status-dnd);
	}
	.dot.offline {
		background: var(--discord-status-offline);
	}

	.dot.online::after,
	.dot.idle::after,
	.dot.dnd::after {
		content: '';
		position: absolute;
		inset: 0;
		border-radius: 50%;
		animation: ping 1.5s infinite;
		opacity: 0.6;
	}

	.dot.offline::after {
		animation: none;
	}

	@keyframes ping {
		0% {
			transform: scale(1);
			opacity: 0.6;
		}
		100% {
			transform: scale(2.5);
			opacity: 0;
		}
	}
</style>
