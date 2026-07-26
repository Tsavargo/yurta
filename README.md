# Dotfiles

Automated Arch Linux setup - package installation and configuration managed through a modular installer framework and GNU Stow.

## How it works

`install.sh` reads package definitions from `packages.conf` and runs the matching installer script in the `installers/` directory. Each installer handles both software installation and configuration:

- **Software** is installed via `pacman`, `curl`, or other package-specific methods
- **Config files** are symlinked into place with [GNU Stow](https://www.gnu.org/software/stow/), pointing into `config/<package>/` which mirrors the target filesystem

Secrets (API keys, tokens) are never stored in the repository. Installers that need them create a template file in `~/.config/fish/conf.d/<package>-env.fish` for you to fill in.

## Usage

```bash
# Install everything
./install.sh

# Install a group
./install.sh base
./install.sh developer
./install.sh desktop

# Install a single package
./install.sh fish
./install.sh claude
```

## Structure

```
├── install.sh              # Orchestration script
├── packages.conf           # Package groups and ordering
├── installers/             # One script per package
│   ├── stow.sh
│   ├── fish.sh
│   ├── claude.sh
│   └── ...
└── config/                 # Stow packages (mirrors target filesystem)
    ├── fish/.config/fish/
    ├── claude/.config/fish/conf.d/
    ├── ghostty/.config/ghostty/
    └── ...
```

## Packages

| Group | Packages |
|-------|----------|
| terminal | ghostty, fish, fonts |
| tools | ssh, firewall, stow, curl, wget |
| sync | syncthing |
| developer | claude, git, zed, database |
| media | mediaplayer |

## Adding a package

1. Create `installers/<name>.sh` - install the software and run `stow` for its config
2. Create `config/<name>/` - mirror the target filesystem path for config files
3. Add `<name>` to the appropriate group in `packages.conf`

For packages that need secrets, have the installer write a template to `~/.config/fish/conf.d/<name>-env.fish` (not tracked by git) rather than placing it in the stow package.
