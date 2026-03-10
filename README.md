# up-modal Vue Example

**[Live demo](https://universal-everything.github.io/up-modal-vue-example/)**

Minimal Vite + Vue 3 demo for [`@lukso/up-modal`](https://www.npmjs.com/package/@lukso/up-modal).

Shows a single **Connect** button that opens the LUKSO Universal Profile connection modal. Once connected, the address is displayed with a **Disconnect** button.

## Setup

```bash
npm install
npm run dev
```

Open http://localhost:5173

## How it works

- [`src/connector.ts`](src/connector.ts) — calls `setupLuksoConnector()` once and exports the `LuksoConnector` promise
- [`src/main.ts`](src/main.ts) — imports the connector (triggers setup early) and mounts the Vue app
- [`src/App.vue`](src/App.vue) — awaits the connector, subscribes to connection state via `watchConnection()`, opens the modal on button click

## Stack

- [Vite](https://vitejs.dev/) + [Vue 3](https://vuejs.org/) + TypeScript
- [`@lukso/up-modal`](https://www.npmjs.com/package/@lukso/up-modal) for the connection modal
