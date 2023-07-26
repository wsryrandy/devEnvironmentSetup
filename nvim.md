## nvim config
### enable copy-paste from system clipboard
Add `opt.clipboard="unnamedplus"` into nvim config file(`~/.config/nvim/lua/custom/init.lua`), make sure `xclip` is on the path (`sudo apt intall xclip`)

### add strings to multi-line use visual mode
- move cursor to the first line
- enter visual block mode (Ctrl + v)
- go to the bottom line
- press capital I
- type in the strings (Note: it will only update the first line on the screen)
- press ESC, it will update all selected lines

# the CHAD NVIM 
## load nvchad
This is a much easier way to setup nvim, but it needs a newer version of nvim from snap, current wsl does not support it well

```
sudo snap install nvim --classic
```
```
alias vim=nvim
echo 'alias vim=nvim' >> ~/.zshrc
```
Need to install one of the Nerd font, currently use JetBrains Mono.

use 
```bash
$ git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
```
for mac and Linux and use 
```bash
$ git clone https://github.com/NvChad/NvChad $HOME\AppData\Local\nvim --depth 1
```
for Windows

May need to install gcc as needed:
```bash
$ sudo apt update
$ sudo apt-get install build-essential
```
## theme
Inside nvchad, type `space + t + h`, and you can choose catppuccin

## Syntax highlighting
`:TSInstallInfo` to find out if the syntax highlighting is available

`:TSInstall cpp` to install new ones

## File tree
`ctrl + n` to open/close file tree

use `m` to mark a file

use `a` to add a new file

use `c` to copy file

use `p` to paste file

use `r` to rename file

## file navigation
use `space + f + f` to find file in all files

`space` is the leader key

use `space + f + b` to find file in opened buffers

## cheatsheet
use `space + c + h` to open and close it

## window navigation
`:vsp` to vertically split and `:sp` to horizontally split
and use `ctrl + h/j/k/l` to navigate through the windows.
Actually, to use together with tmux, will add a plugin to make window movement same as tmux
in `<nvim config>/lua/custom/plugins.lua` add
```lua
{
    "christoomey/vim-tmux-navigator",
    lazy = false,
}
```
Then in `<nvim config>/lua/custom/mappings.lua` add
```lua
M.general = {
    n = {
        ["<C-h"] = {"<cmd> TmuxNavigateLeft<CR>", "window left" },
        ["<C-l"] = {"<cmd> TmuxNavigateRight<CR>", "window right" },
        ["<C-j"] = {"<cmd> TmuxNavigateDown<CR>", "window down" },
        ["<C-k"] = {"<cmd> TmuxNavigateUp<CR>", "window up" },
    }
}
```

## tabb/buffers
`tab` to go to next buffer
`shift + tab` to go to previous buffer
`space + x` to close the current buffer

## terminal
can open terminal in a horizontal way `space + h`
can open terminal in a vertical way `space + v`

actually, it is better to use `alt + h/v` to open or hide a terminal
and `alt + i` will open or hide a floating terminal

## setup additional configs
### custom plugins
For custom plugins, it is better to create an addittional lua file under `<config path>/nvim/lua/custom/plugins.lua`. And add it to `<config path>/nvim/lua/custom/chadrc.lua`:
```lua
local M = {}
M.ui = {theme = 'catppuccin'}
M.plugins = 'custom.plugins'
return M
```
### color column
add the following line to `<config path>/nvim/lua/custom/init.lua`:
```lua
vim.opt.colorcolumn = "80"
```
to enable color column for code

## org mode (**Not work...**)
add following lines in `<config path>/nvim/lua/custom/plugins.lua`:
```lua
local plugins =
{
  {
    "kristijanhusak/org-bullets.nvim",
    requires = {"nvim-treesitter/nvim-treesitter"},
    lazy = false
  },

  {
    "nvim-orgmode/orgmode", 
    lazy = false,
    config = function()
      -- Load custom treesitter grammar for org filetype
      require('orgmode').setup_ts_grammar()
      -- Treesitter configuration
      require('nvim-treesitter.configs').setup {
        -- If TS highlights are not enabled at all, or disabled via `disable` prop,
        -- highlighting will fallback to default Vim syntax highlighting
        highlight = {
          enable = true,
          -- Required for spellcheck, some LaTex highlights and
          -- code block highlights that do not have ts grammar
          additional_vim_regex_highlighting = {'org'},
        },
        ensure_installed = {'org'}, -- Or run :TSUpdate org
      }

      require('orgmode').setup({
        org_agenda_files = {'~/Dropbox/org/*', '~/my-orgs/**/*'},
        org_default_notes_file = '~/Dropbox/org/refile.org',
      })

      require ('cmp').setup({
        sources = {
          { name = 'orgmode' }
        }
      })
    end
  },
}
return plugin
```
Then use `:Lazy sync` to sync the plugins

## setup "nvim-treesitter" in Ubuntu
In `<config path>/nvim/lua/custom/plugins.lua` add following:
```lua
  {
    "nvim-treesitter/nvim-treesitter",
    init = function()
      require("core.utils").lazy_load "nvim-treesitter"
    end,
    cmd = { "TSInstall", "TSBufEnable", "TSBufDisable", "TSModuleInfo" },
    build = ":TSUpdate",
    opts = function()
      return require "plugins.configs.treesitter"
    end,
    config = function(_, opts)
      dofile(vim.g.base46_cache .. "syntax")
      require("nvim-treesitter.install").prefer_git = true
      require("nvim-treesitter.configs").setup(opts)
    end,
  },
```
The line `require("nvim-treesitter.install").prefer_git = true`, will fix the issue

## LSP for c++
In `<config path>/nvim/lua/custom/plugins.lua` add following:
```lua

    {
      "neovim/nvim-lspconfig",
      config = function()
        require "plugins.configs.lspconfig"
        require "custom.configs.lspconfig"
      end
    },
    {
      -- use :MasonInstallAll command to install, or manually install
      "williamboman/mason.nvim",
      opts = {
        ensure_installed = {
          "clangd",
    --      "rust-analyzer",
          "gopls",
          "pyright"
        },
      },
    }
```
The first config is to include configs for lsp. The second one is to install 
lsp servers for neovim using mason, you need to use `:MasonInstallAll`
install the language servers, but sometimes you have to install manually

Create `configs/lspconfig.lua` under custom folder and add following
```lua
local on_attach = require("plugins.configs.lspconfig").on_attach
local capabilities = require("plugins.configs.lspconfig").capabilities

local lspconfig = require "lspconfig"

lspconfig.clangd.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {"c", "cpp"},
  root_dir = lspconfig.util.root_pattern(
    '.clangd',
    '.clang-tidy',
    '.clang-format',
    'compile_commands.json',
    'compile_flags.txt',
    'configure.ac',
    '.git'
  ),
  single_file_support = true,
})

-- lspconfig.rust_analyzer.setup({
--   on_attach = on_attach,
--   capabilities = capabilities,
--   filetypes = {"rust"},
--   root_dir = lspconfig.util.root_pattern(
--     'Cargo.toml'
--   ),
--   single_file_support = true,
--
-- })

lspconfig.gopls.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {"go", "gomod", "gowork", "gotmpl"},
  root_dir = lspconfig.util.root_pattern(
    '.git',
    'go.work',
    'go.mod'
  ),
  single_file_support = true,
})

lspconfig.pyright.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {"python"},
  settings = {
    pyright = {
      autoImportCompletion = true,
    },
    python = {
      analysis = {
        autoSearchPaths = true,
        diagnosticMode = 'openFilesOnly',
        useLibraryCodeForTypes = true,
        typeCheckingMode = 'off',
      },
    },
  },
  single_file_support = true,
})
```

Different language server specification can be found at 
`https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md`.
e.g. Golang uses `gopls`, python uses `pyright`

## NvChad setup for Golang
With all the configs setup above. Need to add format and debug capabillities in this section.

### `gopls` language server setup
Same as last secion with additional settings, extra settings can be found in `gopls` analysis section:
```lua
lspconfig.gopls.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  cmd = {"gopls"},
  filetypes = {"go", "gomod", "gowork", "gotmpl"},
  root_dir = lspconfig.util.root_pattern(
    '.git',
    'go.work',
    'go.mod'
  ),
  settings = {
    gopls = {
      completeUnimported = true,
      usePlaceholders = true,
      analyses = {
        unusedparams = true,
      },
    },
  },
  single_file_support = true,
})
```

### AutoFormatting for Golang
Use `null-ls.nvim` to do auto formatting. Add below to `<config path>/nvim/lua/custom/plugins.lua`:
```lua
  {
    -- null-ls plugin for formatting
    "jose-elias-alvarez/null-ls.nvim",
    ft = "go",
    opts = function()
      return require "custom.configs.null-ls"
    end,
  }
```
Create `null-ls.lua` under `<config path>/nvim/lua/custom/configs/`
```lua
local null_ls = require("null-ls")
local augroup = vim.api.nvim_create_augroup("LspFormatting", {})

local opts = {
  sources = {
    -- go install mvdan.cc/gofumpt@latest
    -- go install -v github.com/incu6us/goimports-reviser/v3@latest
    -- go install github.com/segmentio/golines@latest
    -- Can also try to install those through Mason
    null_ls.builtins.formatting.gofumpt,
    null_ls.builtins.formatting.goimports_reviser,
    null_ls.builtins.formatting.golines,
  },
  on_attach = function(client, bufnr)
    if client.supports_method("textDocument/formatting") then
      vim.api.nvim_clear_autocmds({
        group = augroup,
        buffer = bufnr,
      })
      vim.api.nvim_create_autocmd("BufWritePre",{
        group = augroup,
        buffer = bufnr,
        callback = function()
          vim.lsp.buf.format({ bufnr = bufnr})
        end,
      })
    end
  end,
}
return opts
```
And add `golines`, `gofumpt`, `goimports_reviser` to `Mason` config. The above config also enables autoformatting on save.

### Debugging use dap and delve for Golang
Add `golang` in `treesitter` by `TSInstall go`.
Add `delve` into `Mason` config to install the package. Add below to `<config path>/nvim/lua/custom/plugins.lua`:
```lua
  {
    "mfussenegger/nvim-dap", -- debugger environment for nvim
    init = function()
      require("core.utils").load_mappings("dap") -- add custom key mapping
    end,
  }
```
```lua
  {
    "leoluz/nvim-dap-go", -- golang plugin for dap
    ft = "go",
    dependencies = "mfussenegger/nvim-dap",
    config = function (_, opts)
      require("dap-go").setup(opts)
      require("core.utils").load_mappings("dap_go") -- add custom key mapping
    end
  }
```

#### Add key mapping
Create `<config path>/nvim/lua/custom/mappings.lua` as below
```lua
local M = {}
 -- :DapStepOver for step over in debug
M.dap = {
  plugin = true,
  n = {
    ["<leader>db"] = {
      "<cmd> DapToggleBreakpoint <CR>",
      "Add breakpoint at line"
    },
    ["<leader>dus"] = {
      function ()
        local widgets = require('dap.ui.widgets');
        local sidebar = widgets.sidebar(widgets.scopes);
        sidebar.open();
      end,
      "Open debugging sidebar"
    }
  }
}

M.dap_go = {
  plugin = true,
  n = {
    ["<leader>dgt"] = {
      function()
        require('dap-go').debug_test()
      end,
      "Debug go test"
    },
    ["<leader>dgl"] = {
      function()
        require('dap-go').debug_last()
      end,
      "Debug last go test"
    }
  }
}

return M
```

Add following lines to `<config path>/nvim/lua/custom/chadrc.lua` to include mapping
```lua
 M.mappings = require 'custom.mappings'
```

### `gopher` helper for Golang
Include if-else err and json structure, etc.
Add `gopher` into `Mason` config to install the package. Add below to `<config path>/nvim/lua/custom/plugins.lua`:
```lua
  {
    "olexsmir/gopher.nvim",
    ft = "go",
    config = function(_, opts)
      require("gopher").setup(opts)
      require("core.utils").load_mappings("gopher") -- add mapping
    end,
    build = function ()
      vim.cmd [[silent! GoInstallDeps]] -- install any dependencies this plugin needs
    end
  }
```

`:GoTagAdd json` to add json struct. Same with yaml.

`:GoMod tidy` in `go.mod` file.

`:GoGet` to download packages

`:GoTestsAll` to generate go tests 

`:GoIfErr` to auto-generate err block

Add additional key mapping in `<config path>/nvim/lua/custom/mappings.lua`: 
```lua
M.gopher = {
  plugin = true,
  n = {
    ["<leader>gsj"] = {
      "<cmd> GoTagAdd json <CR>",
      "Add json struct tags"
    },
    ["<leader>gsy"] = {
      "<cmd> GoTagAdd json <CR>",
      "Add json struct tags"
    },
  }
}
```
## NvChad setup for python
Add `pyright` into Mason install and type `:MasonInstallAll` to install the dependencies
```lua
  {
    -- use :MasonInstallAll command to install, or manually install
    "williamboman/mason.nvim",
    opts = {
      ensure_installed = {
        "mypy",
        "ruff",
        "black",
        "pyright",
        "debugpy",
        "lua-language-server",
      },
    },
  }
```
Can use `:LspInfo` function to check if the client is working fine.

### LSP config for Python 
in `<config path>/nvim/lua/custom/configs/lspconfig.lua`

```lua
lspconfig.pyright.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {"python"},
  settings = {
    pyright = {
      autoImportCompletion = true,
    },
    python = {
      analysis = {
        autoSearchPaths = true,
        diagnosticMode = 'openFilesOnly',
        useLibraryCodeForTypes = true,
        typeCheckingMode = 'off',
      },
    },
  },
  single_file_support = true,
})
```
### AutoFormatting for Python
add `python` in `<config path>/nvim/lua/custom/plugins.lua`
```lua
 {
    -- null-ls plugin for formatting
    "jose-elias-alvarez/null-ls.nvim",
    ft = {"go", "python"},
    opts = function()
      return require "custom.configs.null-ls"
    end,
  },

```

in `<config path>/nvim/lua/custom/configs/null-ls.lua`:
```lua
local opts = {
  sources = {
    null_ls.builtins.diagnostics.mypy,
    null_ls.builtins.diagnostics.ruff,
    null-ls.builtins.formatting.black,
  },
 
```
### Debug for Python
in `<config path>/nvim/lua/custom/plugins.lua`:
```lua
 {
     "rcarriga/nvim-dap-ui",
     dependencies = "mfussenegger/nvim-dap",
     config = function()
        local dap = require("dap")
        local dapui = require("dapui")
        dapui.setup()
        dap.listeners.after.event_initialized["dapui_config"] = function()
            dapui.open()
        end
        dap.listeners.before.event_terminated["dapui_config"] = function()
            dapui.close()
        end
        dap.listeners.before.event_exited["dapui_config"] = function()
            dapui.close()
        end
     end
 },
 {
     "mfussenegger/nvim-dap-python",
     ft = "python",
     dependencies = {
         "mfussenegger/nvim-dap",
         "rcarriga/nvim-dap-ui",
     },
     config = function(_, opts)
        local path = "~/.local/share/nvim/mason/packages/debugpy/venv/bbin/python"
        require("dap-python").setup(path)
        require("core.utils").load_mappings("dap_python") 

     end,
 },
```
#### add mapping
in the mapping file:
```lua
M.dap_python = {
  plugin = true,
  n = {
    ["<leader>dpr"] = {
      function()
        require('dap-python').test_method()
      end
    }
  }
}
```
And in the plugins.lua, add loadmappings to nvim-dap-python as shown in the code above


## NvChad setup for cplusplus
