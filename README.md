# pod-dbus

The `dbus` candy of the OpenCharly candy library, as a standalone repo
(kind-prefixed naming). It provides a supervisord-managed D-Bus session bus
exported at a unix-socket address for in-container clients.

## What it provides

Installs the distro D-Bus package (the `dbus-daemon` binary) and runs a session
bus under supervisord as
`dbus-daemon --session --nofork --address=unix:path=/tmp/dbus-session`,
exporting `DBUS_SESSION_BUS_ADDRESS` so notification senders, introspection
tools, and the `dbus:` check verb's `gdbus` clients in the container can reach the
bus.

| Property | Value |
|---|---|
| Service | `dbus` (priority 2, `restart: always`) |
| Requires | `layer-supervisord` |
| Env | `DBUS_SESSION_BUS_ADDRESS=unix:path=/tmp/dbus-session` |
| Package | `dbus` (Arch/Debian/Ubuntu), `dbus-daemon` (Fedora) |

Every claim is verifiable: the `dbus-daemon` binary on disk and the providing
package at build scope, and at deploy scope the session-bus address env var
exported plus the `dbus` service running.

## How to use it

Compose the candy into any box that needs a session bus:

```yaml
my-image:
  candy:
    - '@github.com/opencharly/pod-dbus:<tag>'
```

The `dbus:` check verb drives this bus from a candy/box plan — it is served
out-of-process by `candy/plugin-dbus` (EXEC-based: it drives `gdbus` from
`glib2` over the reverse channel), with no host `charly check dbus` subcommand.
Author `dbus: list` / `call` / `introspect` / `notify` steps and run them with
`charly check live <image> --filter dbus`.

## Layout

- `charly.yml` — the `dbus:` candy entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — this user overview.

## Related

- Closest family skill: `/charly-infrastructure:dbus-layer` — the D-Bus session
  bus candy properties and the notification delivery chain. This candy has no
  `skill:` entity of its own (recorded on
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291)).
- `/charly-check:dbus` — the `dbus:` check verb's full method catalog and YAML
  shape.
- `/charly-infrastructure:supervisord` — the process-manager dependency.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
