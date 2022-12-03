# What is Mentioned Here
How to filter Neovim diagnostic (like below `"randint" is ...`) **with some specific strings**.
- Not kill all diagnostic function.
- Not suppress specific diagnostic level (like hiding less than warning level).

![image](https://user-images.githubusercontent.com/114144822/205418496-51481b74-eacf-40e9-ae93-a51ee390411b.png)

# How to Achieve It
Add below text in `neovim/runtime/lua/vim/diagnostic.lua`, around line number 785. Then, restart Neovim.
```lua
  for i = 1, #line_diags - 1 do
    table.insert(virt_texts, { prefix, virtual_text_highlight_map[line_diags[i].severity] })
  end
  local last = line_diags[#line_diags]

  -- ADDED FROM HERE
  if string.find(last.message, "unbound") or string.find(last.message, "is not accessed") then
    return nil
  end
  -- UNTIL HERE

  -- TODO(tjdevries): Allow different servers to be shown first somehow?
  -- TODO(tjdevries): Display server name associated with these?
  if last.message then
    table.insert(virt_texts, {
      string.format('%s %s', prefix, last.message:gsub('\r', ''):gsub('\n', '  ')),
      virtual_text_highlight_map[last.severity],
    })
```
- If you add `string.find(last.message, "xxx")` with `or` in above code, you can filter `"xxx"` also.
- If you're Mac user, find a location you installed Neovim. And modify `Neovim/share/nvim/runtime/lua/vim/diagnostic.lua`.
- Be careful not to confuse with `neovim/runtime/lua/vim/lsp/diagnostic.lua`. You don't need to put `/lsp/` before `diagnostic.lua`

# Additional
If you modify `local prefix = opts.prefix or '■'` located at the above of previous code, you can change prefix of diagnostic also.
```
local prefix = opts.prefix or '👀'
```
![image](https://user-images.githubusercontent.com/114144822/205419042-51af5a22-93d2-444a-bbec-c5e27589e715.png)

# Note
- I'm not professional of Neovim nor lua. Change code with your responsiblity.
- As of today (3 Dec, 2022), I cannot find this info (= diagnostic filter) on the Internet (in spite of it seems this is normal demands). This code change may harm something?


