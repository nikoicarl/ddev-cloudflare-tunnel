# ddev-cloudflare-tunnel

Share a local [DDEV](https://ddev.com) site with anyone, using a free
[Cloudflare quick tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/do-more-with-tunnels/trycloudflare/).
No Cloudflare account or ngrok account needed.

Installs a single command, `ddev-tunnel`. It's a standalone script, not a
DDEV add-on, so you don't install it with `ddev add-on get`.

```console
$ ddev-tunnel
Tunnelling mysite (http://127.0.0.1:32777)...
Waiting for Cloudflare (log: /tmp/ddev-tunnel-mysite.log)

==================================================
  Public URL: https://random-words-here.trycloudflare.com
  (copied to clipboard)
  Press Ctrl+C to stop.
==================================================
```

## Why

DDEV's built-in `ddev share` uses ngrok, which needs an account and a token.
`ddev-tunnel` uses Cloudflare's quick tunnels instead. They're free and need no sign-up.

It also:

- finds the DDEV project you're in and starts it if it isn't running
- looks up the web container's current port, which changes between restarts
- shows just the public URL instead of cloudflared's full log
- copies the URL to your clipboard
- can stop tunnels from any terminal (`ddev-tunnel stop`)

## Requirements

- [DDEV](https://ddev.readthedocs.io/en/stable/users/install/)
- [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/)
- `python3`, used to read `ddev describe` output
- Optional, for copying the URL: `clip.exe` (WSL), `pbcopy` (macOS), or
  `wl-copy` / `xclip` / `xsel` (Linux)

Tested on WSL2 (Ubuntu). It should also work on macOS and Linux.

## Install

```bash
mkdir -p ~/bin
curl -fsSL https://raw.githubusercontent.com/nikoicarl/ddev-cloudflare-tunnel/main/ddev-tunnel -o ~/bin/ddev-tunnel
chmod +x ~/bin/ddev-tunnel
```

Make sure `~/bin` is on your `PATH`. If it isn't, add this to `~/.bashrc` (or `~/.zshrc`):

```bash
export PATH="$HOME/bin:$PATH"
```

## Usage

```bash
ddev-tunnel                  # tunnel the DDEV project you're in
ddev-tunnel ~/sites/mysite   # tunnel a specific project
ddev-tunnel status           # list running tunnels
ddev-tunnel stop             # stop all tunnels
ddev-tunnel help             # show help
```

Press **Ctrl+C** to stop the tunnel in the terminal where it's running, or
run `ddev-tunnel stop` from any terminal.

cloudflared's full log is written to `/tmp/ddev-tunnel-<project>.log`.

## ⚠️ Anyone with the link can see your site

A quick tunnel is public. Anyone who has the URL can reach your local site,
including its login and admin pages. Before you share the link:

- don't tunnel a site with real user data
- stop the tunnel when you're done
- if you need access control, use a
  [named tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/)
  with Cloudflare Access instead

The URL is random and changes every time you start a tunnel.

## Notes

- `ddev-tunnel stop` stops **all** quick tunnels on the machine. It doesn't
  touch named tunnels (`cloudflared tunnel run`).
- The tunnel connects straight to the web container's port, not through the
  DDEV router. The site sees the real `trycloudflare.com` host name, so links
  it builds point to the tunnel.
- Cloudflare limits quick tunnels and doesn't guarantee they stay up. They're
  for sharing and testing, not production.

## License

[MIT](LICENSE)
