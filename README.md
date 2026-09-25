# herdr-sidebar-mouse

Unofficial patch set for [Herdr](https://github.com/herdrdev/herdr) v0.9.1 that:

1. Highlights a Spaces or Agents sidebar row on mouse hover without focusing or
   switching anything.
2. Steps the selection to the next/previous entry when the mouse wheel is used
   over the Spaces or Agents list, starting from the currently focused entry.
3. Closes a tab when it is double-clicked in the tab bar.

This is a small local build patch. It is not affiliated with or endorsed by the
Herdr project. Herdr is Apache-2.0 licensed; see `LICENSE` and `NOTICE`.

## Base

- Repository: <https://github.com/herdrdev/herdr>
- Tag: `v0.9.1`
- Commit: `065ef9d6a531c49fb8bee7e818ef837065b21ee9`

The patch is generated against that exact commit.

## Files touched

- `src/client/mod.rs`
- `src/client/shell/mouse.rs`
- `src/client/shell/state.rs`
- `src/client/shell/config.rs`
- `src/client/shell/input.rs`
- `src/client/shell/composition.rs`
- `src/client/shell/render.rs`
- `src/client/shell/sidebar.rs`
- `src/client/shell/agent_sidebar.rs`
- `src/client/shell/endpoint_sidebar.rs`
- `src/client/shell/endpoint_agents.rs`
- `src/client/shell/tests/mouse_selection.rs`
- `src/client/shell/tests/agents_worktrees_notifications.rs`
- `src/config/theme.rs`

## Behavior

- Hovering a Spaces or Agents row paints a subtle highlight on that row only. It
  never focuses a workspace, focuses an agent pane, or switches machines. Rows
  that are already focused or keyboard-selected keep their own highlight, so
  hover and selection look different.
- The hover color tracks the desktop theme. With `theme = "terminal"` it uses the
  current [Omarchy](https://omarchy.org) theme surface (`selection`, then
  `muted`, then `lighter_background`) from
  `~/.local/state/omarchy/current/theme/colors.toml` when that file exists, and
  otherwise mixes the terminal's OSC-reported background and foreground. It can
  be overridden with `[theme.custom] hover_bg = "#rrggbb"`.
- Scrolling the wheel over the Spaces list moves from the currently focused
  workspace to the next/previous workspace and focuses it. Over the Agents list
  it moves from the currently focused agent pane to the next/previous pane and
  focuses it. Both directions wrap around. Hovering does not change where the
  wheel steps from.
- The first click on a tab focuses it; a second click within 350 ms closes it.

## Build

Prerequisites:

- Rust 1.98.1 (the version used for the reference build).
- Zig 0.16.0 (needed to build the vendored `libghostty-vt`).

```sh
git clone https://github.com/herdrdev/herdr.git
cd herdr
git checkout 065ef9d6a531c49fb8bee7e818ef837065b21ee9

git apply /path/to/patches/v0.9.1-sidebar-mouse.patch

export ZIG=/path/to/zig-0.16.0/zig
cargo build --release --locked
```

The resulting binary is `target/release/herdr`.

## Test

```sh
export ZIG=/path/to/zig-0.16.0/zig
cargo test --bin herdr --locked client::shell
```

## Install

Back up an existing install first, then replace the binary:

```sh
sudo cp target/release/herdr /usr/bin/herdr
herdr --version
```

Restart the TUI client (detach with `ctrl+b q` and run `herdr` again) so the new
client code is used. A server restart is not required.

## Caveats

- The wheel steps the selection instead of scrolling the list. Use the
  scrollbar or keyboard navigation if you have more entries than fit on screen.
- Hover is visual only: it does not select or focus the hovered row.
- The Omarchy/terminal hover color is read when the client starts (and when the
  terminal color scheme changes). After switching Omarchy theme, restart the
  Herdr client so the new surface is picked up.
- `herdr update` will replace this binary with an official release. Keep a copy
  of the patched binary if you want to keep the behavior.
- Unofficial build. Distributed under the Apache License, Version 2.0; see
  `LICENSE` and `NOTICE`.
