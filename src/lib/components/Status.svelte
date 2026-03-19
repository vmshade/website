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
		if (s === 'dnd') {
			return `I'm currently on ${formatStatus(s)}`;
		}
		return `I'm currently ${formatStatus(s)}`;
	}
</script>

<div class="status">
	<span>{getStatusMessage(status)}</span>
	<span class={`dot ${status}`}></span>
</div>

<style>
	.status {
		display: flex;
		align-items: center;
		gap: 0.4rem;
		width: 100%;
		justify-content: flex-start; /* left aligned */
		color: var(--text-color);
		font-family: sans-serif;
		font-size: 16px;
	}

	.dot {
		width: 10px;
		height: 10px;
		border-radius: 50%;
		position: relative;
	}

	.dot.online {
		background: #a6e3a1;
	}
	.dot.idle {
		background: #f9e2af;
	}
	.dot.dnd {
		background: #f38ba8;
	}
	.dot.offline {
		background: #cdd6f4;
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
