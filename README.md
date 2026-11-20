<p align="center">
  <img src="assets/steamling-logo.svg" alt="Steamling logo" width="180">
</p>

<h3 align="center">Steamling packages</h3>

<p align="center">The package repos Steamling handhelds update from: the kernel, Mesa, gamescope, Governor and everything else Steamling builds itself.</p>

<p align="center">
  <a href="https://github.com/Steamling/steamling"><b>Steamling</b></a> ·
  <a href="https://github.com/Steamling/steamling/blob/main/docs/building/publishing.md"><b>How releases are published</b></a> ·
  <a href="https://github.com/Steamling/packages/releases"><b>Releases</b></a>
</p>

## What this is

Steamling is made of two kinds of packages. Around 1,100 come from Arch Linux ARM, frozen in
[Steamling/base](https://github.com/Steamling/base). The rest Steamling builds itself from the
[main repo](https://github.com/Steamling/steamling): its own kernel, Mesa and gamescope tuned for
each chip, the Steam session, Governor, the emulators and the small tools that tie it together.
Those live here, and a handheld picks up new versions with a normal system update.

There's nothing to download here by hand. pacman reads these releases directly.

## How it's laid out

The repo has no code, only releases, one per pacman repo:

| Release | pacman repo | What's in it |
| --- | --- | --- |
| `sm8550` | `[steamling]` | packages built for the Snapdragon 8 Gen 2 handhelds (the Retroid Pocket 6 and others) |
| `sm8250`, `sm8650`, `sm8750` | `[steamling]` | the same for the other chip families, as they get published |
| `shared` | `[steamling-shared]` | packages that are the same on every handheld |

Each family gets its own release because the kernel, Mesa, gamescope and a few others are built
differently for each chip under the same package name. Every package comes with a signature, and
each release also holds its database and the public key the packages are signed with
(`steamling-repo.gpg`).

## Using it

A Steamling image already has this set up, and a handheld flashed before the repo existed adds it
by itself after one update from your PC. For reference, the sections in `/etc/pacman.conf` look
like this on a Retroid Pocket 6:

```ini
[steamling]
SigLevel = Required DatabaseOptional
Server = https://github.com/Steamling/packages/releases/download/sm8550

[steamling-shared]
SigLevel = Required DatabaseOptional
Server = https://github.com/Steamling/packages/releases/download/shared
```

They sit above the base on purpose: pacman takes a package from the first repo that has it, so
Steamling's Mesa wins over Arch's.

## Can I trust these files?

Every package is signed with Steamling's packaging key, and the handheld checks each one before
installing it (`SigLevel = Required`). The key is set up on the handheld when Steamling is
installed, or with the first update that adds this repo, so a package that wasn't signed with it is
refused.

Some file names have a `_` where you'd expect a `:` (`mesa-2_26.3.1-3-aarch64.pkg.tar.zst`,
for example). That's the package's epoch, and GitHub doesn't allow `:` in file names. Only the name
is different; the signature covers the contents.

## How packages get here

New versions are built on the build machine, then staged and published from the main repo:

```bash
make stage PKG="mesa gamescope"
make publish
```

Staging starts from what's live, swaps in only the packages named and checks that the result can
be installed on every handheld before anything goes up.
[docs/building/publishing.md](https://github.com/Steamling/steamling/blob/main/docs/building/publishing.md)
has the whole routine.

## Licenses

Each package keeps its own license, which you'll find inside it under `/usr/share/licenses`.
Steamling's own code is
[GPL-2.0-or-later](https://github.com/Steamling/steamling/blob/main/LICENSE.md).
