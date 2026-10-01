# GNOME desktop content snap

This snap stages the GNOME desktop package manifest extracted from
`core-base-desktop` and exports it through the `gnome-desktop-content`
content slot. The GNOME session snap consumes it under its own snap
namespace rather than inheriting a custom desktop base.

The package list is based on the Stage 2.3 manifest. GDM and its greeter
runtime are supplied here, while the host systemd unit, `/etc/gdm3`, PAM
definitions, and required host integrations remain owned by core. The
cleanup image has booted with GDM and its workers executing from this
snap, unconfined as intended for this milestone. The GNOME Shell session
continues to run from `ubuntu-desktop-session` under its enforced
AppArmor profile, and the cleanup boot has no failed system or user units.
Core fallbacks for the moved runtime have been removed. Password login,
logout, and a second login were verified before the cleanup stage.
On the final cleanup image, logout returns to a stable GDM user chooser
running GNOME Shell from this snap in `gdm` mode; restarting GDM restores
the confined user session.

The final packed core plus content size is 614,305,792 bytes, which is
1,945,600 bytes below the historical pre-split core. Exact measurements
and runtime verification are recorded in
`~chrb/git/notes/done/20260930-move-gdm-to-the-gnome-content-snap.txt`.

The Ubuntu wallpaper assets are included in the content mount at
`/usr/share/backgrounds`, matching the absolute paths used by GNOME's
background settings. The schema defaults use
`warty-final-ubuntu.png` for light mode and `ubuntu-wallpaper-d.png` for
dark mode.

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
