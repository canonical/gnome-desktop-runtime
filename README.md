# GNOME desktop content snap

This snap stages the GNOME desktop package manifest extracted from
`core-base-desktop` and exports it through the `gnome-desktop-content`
content slot. The GNOME session snap consumes it under its own snap
namespace rather than inheriting a custom desktop base.

The package list is intentionally the full Stage 2.3 manifest, including
`gdm3`, so this first split preserves the existing package set. Moving GDM
into its own display-manager snap remains a later Stage 3A task.

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
