Having used mostly visual Windows editors (MSVS, atom.io, brackets.io)
in the last years here some useful tips for switching to vim.

General tips:
---

Spend 30mins in vimtutor if you never used vim before.

Stay in normal (non-insert) mode whenever possible. Get used to hjkl 
instead of cursor keys, this will likely result in more efficient editing 
over time.

Install https://github.com/tpope/vim-pathogen or vundle for plugins. But
use plugins sparingly at first since builtin vim functionality is likely to
result in more efficient workflows (example NERDTree). Some worthwhile
plugins:
* clang-format
* [a.vim](http://vim.wikia.com/wiki/Easily_switch_between_source_and_header_file)
* [Grep](https://github.com/yegappan/grep) for cmdline syntax grep instead
  of :vimgrep (personal preference)
* [Command-T](http://www.vim.org/scripts/script.php?script_id=3025)
* more plugins [here](http://www.vim.org/scripts/script_search_results.php?keywords=&script_type=&order_by=downloads&direction=descending&search=search)

Check out http://vim.wikia.com/wiki/Vim_Tips_Wiki.

My most frequent shortcuts & cmds in normal mode:
---

* hjkl
* (inoremap) jk instead of ESC
* w e b W E B for word navigation
* 0 ^ $ for intra-line navigation
* 123G ctrl-g / ? for inter-line navigation
* x 3x dd dw d3w to del in normal mode
* cw c3w - change word
* ci' ci" cib - change in quotes and brackets
* i I a A o O for entering insert mode
* v/V y/d p/P for copy paste
* u ctrl-r for undo/redo
* :edit <filename>; :ls; :b <filename substr>; :b #; ctrl-g for multifile edits 
  (alternatively use tabs: :tabe <filename> gt  gT ..., but using buffers
  seems to be preferred)
* :A (with A.vim)
* gf to open #include file under cursor (assuming cwd is correct)
* :make :copen :ccl :cn :cb (or jk<enter>) for quickfix window (navigate
  to lint or compiler errors quickly)
* :vimgrep <expr> <glob> so you can quickfix grep results