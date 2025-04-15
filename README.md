
# The problem

The ts_ls (typescript lsp) `vim.lsp.buf.hover()` is in many cases completely useless:

![image](https://github.com/user-attachments/assets/a30b638a-2d06-4861-9330-0375a2c4a828)

## The solution

This plugin improves `vim.lsp.buf.hover()` by actually showing the exact declaration of the `interface` or `type` (crazy how that's not the default 🤔). 

Additionally, you can press the sign on the line to open yet another window with the declaration of the "nested type":


![somethign](https://gyazo.com/7ea66b405b1999248e7e145dc90cdd5a.gif)

#### Disclaimer

This plugin is really only meant to be used on `interface` or `type` in typescript. It can also be used on pretty much anything else where `vim.lsp.buf.hover()` is applicable. But be aware that if used on anything else that it might not always work, there is simply too many other such cases for me to be able to cover all of them. I intend to slowly add such cases as I discover them. 

# Installation

Lazy.nvim
```lua
{
  "Sebastian-Nielsen/better-type-hover",
  config = function()
    require("better-type-hover").setup() 
  end,
}
```

# Config

These are all the default options for reference:

```lua
require("better-type-hover").setup({
	-- If the declaration in the window is longer than 20 lines remove all lines after the 20th line. 
	fold_lines_after_line = 20,
	-- The primary key to hit to open the main window
	openTypeDocKeymap = "<C-P>",
	-- These letters/digits are used in order
	keys_that_open_nested_types = { 'a', 's', 'b', 'i', 'e', 'u', 'r', 'x' },
	-- This is to avoid a type hint (i.e. a letter) showing up in the main window
	types_to_not_expand = {"string", "number", "boolean", "Date"}
})
```

# Known issues

- [ ] Opening the popup too close to the edge of the screen: https://gyazo.com/df079cebabee1526318c12bf71c5242c
- [ ] Handle that a nested type is e.g. `style?: StyleHTMLAttributes<HTMLDivElement> & CSSProperties;`. Currently, it only expands the left-most (`StyleHTMLAttributes<HTMLDivElement>` in this case). This one is hard to solve because you could in theory have something like `style?: A & (B | (C & D)) | E & F & ...` and which one is it expected to open here? And how would you prompt the user to choose one? 
- [ ] When opening a requested type, it's currently hardcoded to always request the doc-definition at column one of the type, this is incorrect in case of e.g. `JSX.Element` as that would fetch the doc of `JSX` where it instead actually should had returned the doc for `Element`. So insert logic that checks if the type contains a dot and if it does then trigger the doc-definition on the first column to the right of the last dot. https://gyazo.com/be0374a6b75e3086c2dee4d095f24666
- [ ] If you put a key that expects a motion immediately afterwards (e.g. `d`, `c`, `g`, `f`, `t`) into `keys_that_open_nested_types`, then you are going to experience a delay when triggering it: https://gyazo.com/a28e06b2689a99264882ff7b67297009. 

# Todo:

- [ ] Feature idea: Be able to indefintely expand nested_types. Show letter-hints in the second window so that you can expand nested_types in the second window. When a nested_type in the second window is requested to be expanded, the content of the second window will be replaced by the requested declaration. 
