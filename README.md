# GNOME desktop content snap

This snap stages the GNOME desktop package manifest extracted from
`core-base-desktop` and exports it through the `gnome-desktop-content`
content slot. The GNOME session snap consumes it under its own snap
namespace rather than inheriting a custom desktop base.

The package list is based on the Stage 2.3 manifest. GDM migration is being
staged: `gdm3` and its greeter data are supplied here, while the host
systemd unit, `/etc/gdm3`, and GDM PAM service definitions remain owned by
core. The host-managed, unconfined GDM and greeter runtime now launch from
this snap, with core fallbacks retained until cleanup. A fresh image boots
to the confined GNOME session, and the greeter Shell has been verified
from this snap with autologin disabled. Password login, logout, and a
second login were verified before starting cleanup of the duplicate core
payload. See
`~chrb/git/notes/todo/20260930-move-gdm-to-the-gnome-content-snap.txt`.

The Resolute wallpapers are included in the content mount. The session snap
binds them at `/usr/share/backgrounds`, matching the absolute paths used by
GNOME's background settings.

`fonts-ubuntu` is staged in the content snap so confined applications can
resolve Ubuntu's default `Ubuntu Sans Mono` terminal font.

`rust-coreutils` is staged so terminal shells resolve commands such as
`dircolors` from the content mount using the same implementation as Ubuntu
26.04, instead of falling through to inaccessible host paths. The content
snap primes the same `/usr/bin/dircolors` symlink as the Ubuntu system.

The portal frontend is rebuilt from the staged Ubuntu source with the
project's deferred-backend-activation patch. This keeps the content-snap
session from reintroducing the portal startup delay fixed in
`core-base-desktop`; keep the patch synchronized between the two projects.

The snap also runs the user-scoped PipeWire daemon and WirePlumber session
manager. It exports the PipeWire socket through the `pipewire` slot, and the
desktop session connects its portal and screencast services to that slot.
WirePlumber connects to the desktop session's portal permission store.

Build locally with `./go-build`.
