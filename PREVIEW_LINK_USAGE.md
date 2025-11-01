# Preview Link Feature for obsidian.nvim

This feature adds a preview command to obsidian.nvim that shows linked notes in a floating window without navigating away from your current note.

## Requirements

- `rmagatti/goto-preview.nvim` plugin must be installed

## Installation

The feature is already integrated into obsidian.nvim. Make sure you have both plugins installed:

```lua
-- In your plugin manager (lazy.nvim example)
{
  'epwalsh/obsidian.nvim',
  dependencies = {
    'nvim-lua/plenary.nvim',
    'rmagatti/goto-preview.nvim',
  },
}

{
  'rmagatti/goto-preview.nvim',
  config = function()
    require('goto-preview').setup({
      width = 120,
      height = 20,
      border = {"↖", "─", "┐", "│", "┘", "─", "└", "│"},
      default_mappings = false,
      opacity = nil,
    })
  end,
}
```

## Usage

### Command

Place your cursor on any wiki-style or markdown link and run:

```vim
:ObsidianPreviewLink
```

### Recommended Keymap

Add to your Neovim config:

```lua
vim.keymap.set('n', 'gpl', ':ObsidianPreviewLink<CR>', { desc = 'Preview link' })
```

Or use it with your existing Obsidian keymaps:

```lua
vim.keymap.set('n', '<leader>op', ':ObsidianPreviewLink<CR>', { desc = 'Preview link' })
```

## Supported Link Types

The preview command works with:

- **Wiki links**: `[[note-name]]`
- **Wiki links with aliases**: `[[note-name|Display Text]]`
- **Wiki links with aliases from frontmatter**: `[[alias-name]]` (resolves to the actual note)
- **Markdown links**: `[Display Text](note-name.md)`
- **Links with block references**: `[[note#^block-id]]` (jumps to the block)
- **Links with anchors**: `[[note#heading]]` (jumps to the heading)

## How It Works

1. **Link Detection**: Gets the link under your cursor
2. **Link Resolution**: Uses Obsidian's robust resolution system to find the note
   - Searches by note ID
   - Searches by filename
   - Searches by aliases (from frontmatter)
   - Handles fuzzy matching
3. **Preview Display**: Opens the note in a floating window
   - Automatically jumps to block references or anchors if present
   - Uses goto-preview's window management
   - Respects all goto-preview configuration settings

## Advantages Over Follow Link

- **Stay in Context**: Preview without leaving your current note
- **Quick Reference**: Peek at linked content without disrupting your flow
- **Stack Windows**: With goto-preview's stack feature, you can open multiple previews
- **Smart Resolution**: Uses Obsidian's full resolution system including aliases

## Disambiguation

If a link resolves to multiple notes (rare but possible with fuzzy matching):
- The command will warn you and suggest using `:ObsidianFollowLink` first
- Follow the link to disambiguate which note you meant
- Then use preview on subsequent references

## Example Workflow

In your daily note:

```markdown
# 2025-11-01

Today I worked on [[project-x]] and reviewed [[meeting-notes]].

Also need to check [[tasks#urgent]] later.
```

Place cursor on `[[project-x]]` → Press `gpl` → Preview opens!

Place cursor on `[[tasks#urgent]]` → Press `gpl` → Preview opens at the "urgent" heading!

## Integration with LSP

The preview command works alongside Obsidian's existing LSP integration. You can:
- Use `gd` or LSP definition to follow links (opens in current/split buffer)
- Use `gpl` to preview links (opens in floating window)

## Configuration

The preview window appearance is controlled by goto-preview configuration:

```lua
require('goto-preview').setup({
  width = 120,              -- Preview window width
  height = 20,              -- Preview window height
  border = "rounded",       -- Border style
  opacity = nil,            -- Window opacity (0-100)
  focus_on_open = true,     -- Focus preview when opened
  dismiss_on_move = false,  -- Close on cursor move
  zindex = 1,               -- Window stacking order
})
```

## Troubleshooting

**Error: "goto-preview plugin not found"**
- Install the goto-preview.nvim plugin
- Make sure it's loaded before using the preview command

**Warning: "no valid link to preview"**
- Place cursor directly on a link (between `[[` and `]]`)
- Make sure the file is recognized as markdown (`:set ft=markdown`)

**Warning: "note not found"**
- The linked note doesn't exist yet
- Create the note first with `:ObsidianFollowLink` or `:ObsidianNew`

**Warning: "found N matches"**
- Multiple notes match the link text
- Use `:ObsidianFollowLink` to select the intended note
- Consider using more specific note names or IDs
