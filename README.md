# kickstart-init-lua

Personal Neovim config based on [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim), single `init.lua` using the built-in `vim.pack` plugin manager.

## Requirements

### Neovim >= 0.12

`vim.pack` (the built-in plugin manager this config uses) requires Neovim 0.12 or newer.

- **Arch:** `sudo pacman -S neovim` (repositories are current)
- **Ubuntu:** the apt package is too old. Install from the GitHub release instead:

```sh
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz
sudo ln -sf /opt/nvim-linux-x86_64/bin/nvim /usr/local/bin/nvim
```

### System packages

| Package | Why it's needed |
| --- | --- |
| `git` | vim.pack clones plugins from GitHub |
| `make` + `gcc` | builds telescope-fzf-native, LuaSnip's jsregexp, and treesitter parsers |
| `nodejs` + `npm` | Mason installs ts_ls, html, cssls, prettier, perlnavigator from npm |
| `ripgrep` | Telescope live grep (`<leader>sg`) |
| `unzip` / `curl` / `tar` / `gzip` | Mason downloads and extracts tools |
| `lazygit` | `<leader>g` floating lazygit window |
| `perl-tidy` (Arch) / `perltidy` (Ubuntu) | Perl formatter (format-on-save, `<leader>f`) |
| `perl-perl-critic` / `libperl-critic-perl` | Perl linting (perlcritic) |
| `perl` | needed by perlcritic and perlnavigator's syntax check |
| `go` | only needed if you write Go (gopls/golangci-lint are Mason-installed) |
| `systemd-analyze` | linting for `.service` files (already present on systemd-based distros) |

### Ubuntu

```sh
sudo apt update
sudo apt install -y git build-essential nodejs npm ripgrep unzip curl tar \
  perltidy libperl-critic-perl perl golang-go

# lazygit is not in the Ubuntu repos; install from the GitHub release:
LAZYGIT_VERSION=$(curl -s https://api.github.com/repos/jesseduffield/lazygit/releases/latest | grep -Po '"tag_name": "v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit /usr/local/bin && rm lazygit lazygit.tar.gz
```

### Arch

```sh
sudo pacman -S --needed neovim git base-devel nodejs npm ripgrep unzip curl \
  lazygit perl-tidy perl-perl-critic perl go
```

### Optional

- A [Nerd Font](https://www.nerdfonts.com/) selected in your terminal — then set `vim.g.have_nerd_font = true` in `init.lua` for icons.

## Installing the config

```sh
# Back up any existing config first
mv ~/.config/nvim ~/.config/nvim.bak 2>/dev/null

mkdir -p ~/.config/nvim
curl -o ~/.config/nvim/init.lua https://raw.githubusercontent.com/bpiroman/kickstart-init-lua/main/init.lua
```

Or clone the repo and symlink, so updates are a `git pull` away:

```sh
git clone git@github.com:bpiroman/kickstart-init-lua.git ~/Projects/kickstart-init-lua
mkdir -p ~/.config/nvim
ln -sf ~/Projects/kickstart-init-lua/init.lua ~/.config/nvim/init.lua
```

## First run

On the first `nvim` launch, vim.pack downloads all plugins and Mason auto-installs the LSP servers and tools (gopls, ts_ls, html, cssls, lua_ls, stylua, perlnavigator, prettier, goimports, golangci-lint). Treesitter parsers compile on demand — this all needs a few minutes and network access. Run `:checkhealth` if anything looks off.

To update plugins later: `:lua vim.pack.update()`
