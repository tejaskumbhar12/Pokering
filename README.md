# Pokering ♠

Real-time **planning poker** for agile refinement / estimation meetings. Everyone joins a room, votes on story points with Fibonacci cards, and the cards reveal automatically once all voters are in.

## Features

- **Rooms by name** — share a link, teammates join instantly.
- **Hidden voting → auto reveal** — cards stay face-down until every voter has picked, then flip together.
- **Per-user re-vote** — after a reveal, changing your card hides only *your* card; everyone else stays revealed until they change too.
- **Results chart** — a bar chart of votes per estimate, with the most-voted value highlighted.
- **Consensus confetti** — a confetti burst when everyone agrees.
- **Spectator mode** — a "Spectate" toggle lets PO/QA watch without voting; spectators never block the reveal.
- **Kick** — anyone can remove anyone from the room.
- **Reactions** — hover a player to send emoji reactions.
- **Share link** — one-click copy of a room invite URL (`?room=<name>`).
- Responsive, fits the viewport (no page scroll), light theme inspired by a construction MPG dashboard.

## Tech stack

| Layer | Tech |
|-------|------|
| Runtime | Node.js (>= 18, tested on 20) |
| Server | Express — serves static files |
| Real-time | Socket.IO (WebSocket) |
| Frontend | Vanilla HTML / CSS / JavaScript (no build step, no framework) |
| State | In-memory (per-room `Map` on the server) |
| Deploy | Render.com (`render.yaml` blueprint included) |

No database, no bundler, no framework — plain files served by Express with Socket.IO for live sync.

## Project structure

```
Pokering/
├── server.js          # Express + Socket.IO server, all room/game logic
├── package.json
├── render.yaml        # Render deploy blueprint
└── public/
    ├── index.html     # Login + room UI
    ├── app.js         # Client: sockets, voting, rendering
    ├── style.css      # Theme and layout
    └── preview.html   # Static visual mock (not used at runtime)
```

## Run locally

Requires [Node.js](https://nodejs.org) 18+.

```bash
npm install
npm start
```

Open http://localhost:3000. To test with multiple people on one machine, open extra tabs or an incognito window and join the same room name.

**Colleagues on the same network** open `http://<your-machine-ip>:3000` (find it with `ipconfig` on Windows / `ifconfig` on macOS/Linux). Allow the firewall prompt on first access.

## How to use

1. Enter a **name** and a **room** name, click **Enter room**.
2. Share the room: click **🔗 Share link** (top bar) and send the copied URL — it pre-fills the room for whoever opens it.
3. **Vote** by clicking a card (0, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ?, ☕). Your card stays hidden to others until everyone has voted.
4. When all voters have voted, cards **reveal** automatically and the results chart appears. Full agreement triggers confetti.
5. Click **New round** to clear votes and estimate the next item. Picking a new card after a reveal also starts a fresh round for you.
6. Flip the **👁 Spectate** switch to watch without voting (and back to join in).
7. Hover a player for **reactions** or to **kick** them.

## Deploy (Render)

This app needs a persistent WebSocket server, so it runs on a host that keeps a Node process alive (Render, Railway, Fly.io, …) — **not** on static-only hosts like Netlify/GitHub Pages.

On [Render](https://render.com):

1. Push this repo to GitHub.
2. **New +** → **Blueprint** → select the repo. Render reads `render.yaml` and fills in the build (`npm install`) and start (`node server.js`) commands.
3. **Apply**, wait for the build, and open the generated `https://<name>.onrender.com` URL.

Every push to `main` auto-deploys. The free tier sleeps after ~15 min idle (first hit cold-starts in ~30–50 s).

## Notes

- Room state lives in memory, so a server restart / redeploy clears all active rooms — deploy between meetings, not mid-refinement.
- `PORT` is read from the environment (`process.env.PORT`), defaulting to `3000`.
