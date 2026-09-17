# Project Context

## Project Overview

This repository is the root workspace for competitive-programming practice. Solutions are written as standalone C++20 files and tested locally before submission to online judges.

## Architecture

- Neovim configuration is managed in `/Users/raj/dotfiles`.
- Homebrew GCC 16 at `/opt/homebrew/bin/g++-16` compiles C++ solutions with GNU libstdc++, including `bits/stdc++.h` and PBDS.
- `.clangd` makes Mason clangd parse the same GCC 16 standard-library headers while retaining clangd's parser and editor features.
- The Neovim task engine stores hashed run/debug executables under Neovim's cache and removes them after use.
- CompetiTest stores executables in a per-Neovim-process cache directory and removes that directory when Neovim exits.
- `input.txt` remains available for the single-file run workflow; programs read stdin and write stdout without hard-coded `freopen` calls.

## Decisions

- Use Homebrew `g++-16` for C++ compile, run, test, and debug workflows. Status: active.
- Keep Apple system compilers and SDK directories untouched. Status: active.
- Keep clangd's GCC compatibility configuration local to this workspace rather than changing every C++ project. Status: active.
- Keep generated executables outside the repository. Status: active.

## Current Tasks

- No active implementation task.

## Completed Work

- Added workspace clangd flags aligned with the installed GCC 16 target, macOS 26 SDK, and libstdc++ headers.
- Configured the shared Neovim workflow to use GCC 16 for C++ runs, CompetiTest, and CodeLLDB debug builds.
- Removed hard-coded stdin/stdout file redirection from the shared C++ snippet.

## Important Discoveries

- `/usr/bin/g++` is Apple Clang and cannot resolve GNU `bits/stdc++.h`.
- The installed Homebrew GCC 16 bottle targets `aarch64-apple-darwin25` and uses `/Library/Developer/CommandLineTools/SDKs/MacOSX26.sdk`.
- clangd needs `-nostdinc++` plus the matching GCC target, SDK, and libstdc++ paths; query-driver alone mixed incompatible SDK/libc++ paths on this machine.
- CodeLLDB can debug GCC-generated Mach-O binaries, including breakpoints, stepping, primitive variables, and formatted `std::vector` values. PBDS remains visible as raw internals.

## Technical Debt

- `.clangd` contains GCC-major, target, and SDK-specific paths. Revalidate it after Homebrew GCC or Command Line Tools upgrades.
- Legacy VS Code tasks still use Apple Clang and create executables beside source files; they are outside the Neovim workflow addressed here.

## Open Questions

- Should the legacy VS Code task and launch configuration be aligned with the GCC/cache workflow later?

## Working Agreements

- Programs read from stdin and write judged output to stdout.
- Debug logging must use stderr and must not contaminate stdout.
- Do not copy GNU headers into Xcode, macOS SDKs, or `/usr/include`.
- Do not set global `CPATH` or `CPLUS_INCLUDE_PATH` overrides.
- Do not commit generated executables, debug symbols, or editor caches.

## Handoff Notes

Current objective: maintain a fast, GNU-compatible Neovim workflow for standalone C++20 competitive-programming solutions.

Current status: GCC compilation, clangd parsing, CompetiTest execution/cache cleanup, and CodeLLDB debugging are validated.

Blockers: none.

Next steps after a compiler/toolchain upgrade:

1. Run `/opt/homebrew/bin/g++-16 -dumpmachine`, `-print-sysroot`, and `-dumpversion`.
2. Update `.clangd` if the GCC major version, target, or SDK changes.
3. Run Mason clangd's `--check` against a source using `bits/stdc++.h`, `std::vector`, and PBDS.
4. Run a CompetiTest sample and a CodeLLDB breakpoint smoke test.

Relevant files:

- `.clangd`
- `context.md`
- `/Users/raj/dotfiles/.config/nvim/lua/core/tasks.lua`
- `/Users/raj/dotfiles/.config/nvim/lua/plugins/misc.lua`
- `/Users/raj/dotfiles/.config/nvim/lua/plugins/luasnip.lua`
