# Kestrel desktop

Packaging for Kestrel's embedded desktop set: the `kestrel-desktop` metapackage
and `kestrel-desktop-libs`, the rebuild of Hyprland's shared library closure.

Kestrel is Hyprland + Quickshell. Those two, and the libraries they share, are
owned and shipped as one matched set. Users are not supported in swapping them;
enforcement is packaging-level only (see the master plan, Part D).

## Packages

| Package | Provides | Notes |
|---|---|---|
| `kestrel-desktop` | (metapackage) | depends on the set; conflicts with alternative compositors/shells |
| `hyprutils-kestrel` | `hyprutils`, `libhyprutils.so=13-64` | |
| `hyprlang-kestrel` | `hyprlang`, `libhyprlang.so=2-64` | |
| `hyprgraphics-kestrel` | `hyprgraphics`, `libhyprgraphics.so=4-64` | |
| `hyprcursor-kestrel` | `hyprcursor`, `libhyprcursor.so=0-64` | |
| `hyprwire-kestrel` | `hyprwire`, `libhyprwire.so=3-64` | |
| `aquamarine-kestrel` | `aquamarine`, `libaquamarine.so=14-64` | DRM backend library |

Each library package `conflicts`/`replaces` the Arch package and `provides` its
soname, so Arch cannot overwrite it and consumers (for example `hyprlock`) stay
satisfied.

## Why

Arch builds these libraries with plain generic `-O2` and no LTO, while Kestrel's
Hyprland and Quickshell are built `-march=x86-64-v3` with ThinLTO. Rebuilding the
closure the same way gives the whole desktop one codegen. Upstream versions are
kept exactly (they match Arch's), so there is no version divergence; see the
master plan Part E2 for the measured size deltas.

## Building

```
cd packaging/hyprutils            # or any library
makepkg -s -f --noconfirm --nocheck
```

The metapackage has no build-time dependencies; build it with `--nodeps`:

```
cd packaging/kestrel-desktop
makepkg -f --nodeps --noconfirm --nocheck
```

## Publishing

`kestrel-distro/packages` builds and signs these with its publish workflow and
serves them from `[kestrel]` (`https://kestrel-distro.github.io/packages/`).

## Standards

- Changes ship with tests where the project can test them.
- English in commits, code, and documentation.
- History is never rewritten.
