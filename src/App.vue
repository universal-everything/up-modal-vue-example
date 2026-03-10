<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { getConnection, disconnect } from '@wagmi/core'
import { connectorPromise } from './connector'

const address = ref<string | null>(null)
const connector = ref<Awaited<typeof connectorPromise> | null>(null)
const ready = ref(false)
let unwatch: (() => void) | null = null

onMounted(async () => {
  connector.value = await connectorPromise
  const { wagmiConfig } = connector.value

  function syncAddress() {
    const conn = getConnection(wagmiConfig)
    address.value = conn?.isConnected ? conn.address ?? null : null
  }

  // setupLuksoConnector awaits reconnect, so connection is settled by now
  syncAddress()
  ready.value = true
  unwatch = wagmiConfig.subscribe(
    (s) => s.status,
    syncAddress,
  )
})

onUnmounted(() => {
  unwatch?.()
})

function openModal() {
  connector.value?.showSignInModal()
}

async function handleDisconnect() {
  const { wagmiConfig } = connector.value!
  await disconnect(wagmiConfig)
  address.value = null
}

function truncate(addr: string) {
  return `${addr.slice(0, 6)}…${addr.slice(-4)}`
}
</script>

<template>
  <main>
    <div v-if="ready && address" class="connected">
      <p class="label">Connected as</p>
      <p class="address" :title="address">{{ truncate(address) }}</p>
      <button class="btn btn-secondary" @click="handleDisconnect">Disconnect</button>
    </div>
    <div v-else-if="ready" class="disconnected">
      <button class="btn btn-primary" @click="openModal">Connect</button>
    </div>
  </main>
</template>

<style scoped>
main {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: sans-serif;
}

.connected,
.disconnected {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.label {
  margin: 0;
  font-size: 0.875rem;
  color: #666;
}

.address {
  margin: 0;
  font-size: 1.25rem;
  font-weight: 600;
  font-family: monospace;
  letter-spacing: 0.05em;
}

.btn {
  padding: 10px 24px;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: opacity 0.15s;
}

.btn:hover {
  opacity: 0.85;
}

.btn-primary {
  background: #fe005b;
  color: #fff;
}

.btn-secondary {
  background: #eee;
  color: #333;
}
</style>
