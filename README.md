# Kestrel desktop

Packaging for Kestrel's embedded desktop set: the `kestrel-desktop`
metapackage and the rebuilt Hyprland shared-library closure.

Kestrel is Hyprland + Quickshell. Those two and the libraries they share are
owned and shipped as one matched set. Users are not supported in swapping them;
the enforcement is packaging-level: distinct `-kestrel` names that
`conflicts`/`replaces` the Arch package and `provides` its soname, so Arch
cannot overwrite a Kestrel library and consumers such as `hyprlock` stay
satisfied.

## Packages

| Package | Provides | Notes |
|---|---|---|
| `kestrel-desktop` | metapackage | Pulls in the matched desktop set |
| `aquamarine-kestrel` | `aquamarine`, `libaquamarine.so=14-64` | DRM backend library |
| `hyprutils-kestrel` | `hyprutils`, `libhyprutils.so=13-64` | |
| `hyprlang-kestrel` | `hyprlang`, `libhyprlang.so=2-64` | |
| `hyprgraphics-kestrel` | `hyprgraphics`, `libhyprgraphics.so=4-64` | |
| `hyprcursor-kestrel` | `hyprcursor`, `libhyprcursor.so=0-64` | |
| `hyprwire-kestrel` | `hyprwire`, `libhyprwire.so=3-64` | |
| `qt6-base-kestrel` | `qt6-base` | Qt base with client-side `linux-drm-syncobj-v1` (Wayland explicit sync) from `kestrel-distro/qtbase` |

The Hyprland and Quickshell binaries themselves live in their own repositories
(`hyprland-kestrel`, `quickshell-kestrel`). This repository builds the shared
closure they link against.

## Why

Arch builds these libraries with generic `-O2` and no LTO, while Kestrel's
Hyprland and Quickshell are built `-march=x86-64-v3` with ThinLTO. Rebuilding
the closure the same way gives the whole desktop one codegen. Upstream versions
are kept exactly (they match Arch's), so there is no version divergence and no
API skew.

qtbase carries the Wayland client Quickshell presents through; since Qt 6.12
the explicit-sync client lives there. The `kestrel/6.12.0-syncobj` branch in
`kestrel-distro/qtbase` adds that support.

## Building

```bash
cd packaging/hyprutils            # or any library
makepkg -s -f --noconfirm --nocheck

cd packaging/qt6-base
makepkg -s -f --noconfirm --nocheck
```

The metapackage has no build-time dependencies; build it with `--nodeps`:

```bash
cd packaging/kestrel-desktop
makepkg -f --nodeps --noconfirm --nocheck
```

The ISO builder consumes these through
`kestrel/install/kestrel-source.packages` (`name=url#subdir`), so a local ISO
build compiles the same recipes.

## Publishing

`kestrel-distro/packages` builds and signs these with its publish workflow and
serves them from the signed `[kestrel]` repository
(`https://kestrel-distro.github.io/packages/`). A desktop-only publish keeps the
existing kernel packages.

## Layout

| Path | Contents |
|---|---|
| `packaging/aquamarine` | aquamarine |
| `packaging/hyprutils` | hyprutils |
| `packaging/hyprlang` | hyprlang |
| `packaging/hyprgraphics` | hyprgraphics |
| `packaging/hyprcursor` | hyprcursor |
| `packaging/hyprwire` | hyprwire |
| `packaging/qt6-base` | Qt base + explicit sync |
| `packaging/kestrel-desktop` | metapackage |

## Standards

- Changes ship with tests where the project can test them.
- English in commits, code and documentation.
- History is never rewritten.
- Everything is exercised in the VM first.

## Related

[`kestrel`](https://github.com/kestrel-distro/kestrel) ·
[`iso`](https://github.com/kestrel-distro/iso) ·
[`hyprland`](https://github.com/kestrel-distro/hyprland) ·
[`quickshell`](https://github.com/kestrel-distro/quickshell) ·
[`qtbase`](https://github.com/kestrel-distro/qtbase) ·
[`packages`](https://github.com/kestrel-distro/packages)
