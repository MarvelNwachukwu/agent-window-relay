# Agent Window Relay

A Chrome extension that lets the [omp](https://github.com/can1357/oh-my-pi) coding agent use your real, logged-in Chrome **from a window of its own**, so it never takes over the window you're working in.

It's a modified build of omp's own _OMP Browser Relay_ extension. It speaks the same protocol and connects to the same local relay that omp starts (`omp browser-relay`, port 9224).

## What's different from the stock extension

- **The agent gets its own window.** New agent tabs open in a separate Chrome window that opens without taking focus. Put it on another monitor and it stays there: when the agent closes its last tab, a New Tab page is left behind so the window isn't closed.
- **Drag to choose the window.** Drag one of the agent's tabs into any window, and its next tabs open there.
- **No window ever comes to the front.** Activating a tab, `page.bringToFront()` included, only selects it inside its own window. The stock extension focuses the window, pulling it over whatever you're doing.
- **Tab groups stay put.** The stock extension creates the agent's "omp" tab group in your focused window, moving the agent's tab into it. Here the group is created in the tab's own window.
- **Setting:** _Open omp's tabs in_ → "A window of their own" (default) or "The window you're using" (opened unselected).

## Install

1. Clone this repo.
2. Open `chrome://extensions`, turn on **Developer mode**, click **Load unpacked**, and pick the `extension/` folder.
3. Disable or remove the stock _OMP Browser Relay_ extension if you have it. Two relay extensions in one Chrome both connect to the relay and fight over the same tabs.
4. Use omp's browser with the relay: `browser.open({ name: "x", app: { relay: true } })`, or `omp config set browser.relay true`.

The toolbar badge shows **on** while connected to the relay.

### omp version

Use omp **18.12.1 or newer** (`omp update`). Older versions don't open a tab of their own: an open without `app.target` takes over the tab you're looking at, and no extension can prevent that.

### Notices from omp

omp may say the extension is out of date and suggest `omp browser-relay install`. That command installs the stock extension into `~/.omp/browser-relay/extension`; it doesn't touch this one, and you can ignore the notice. If omp instead refuses to connect with an "out of date" error, its relay protocol changed and this extension needs updating.

## Settings

Click the toolbar icon:

| Setting | Default | Notes |
| --- | --- | --- |
| Relay port | 9224 | Must match `omp browser-relay --port` |
| Token | empty | Must match `omp browser-relay --token` |
| Open omp's tabs in | A window of their own | Or "The window you're using" |

## Agent skill

[`skills/agent-window-relay`](skills/agent-window-relay/SKILL.md) teaches the agent how to use your Chrome through this extension:
- open its own tabs in the agent window
- borrow your tabs only when you point at one
- ask before sending, buying or deleting
- hand logins and 2FA back to you
- never bring a tab to the front

Install it by linking it into a skills folder your agent reads. omp reads `~/.agents/skills`, and Claude Code reads `~/.claude/skills`:

```sh
ln -s "$PWD/skills/agent-window-relay" ~/.agents/skills/agent-window-relay
```

## Security

Anything that can reach the relay port can drive your logged-in browser. The relay only listens on loopback; set a token (`omp browser-relay --token <secret>`, mirrored in the settings) if you don't trust other local processes.

## License

MIT. Derived from oh-my-pi's browser relay extension; see [LICENSE](LICENSE).
