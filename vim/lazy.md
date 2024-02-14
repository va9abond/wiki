                                               | lazy.nvim |

`lazy.nivm` - менеджер плагинов для `neovim`. Он позволяет загружать плагины
"лениво", т.е. только в тот момент, когда этот плагин нужен, чтобы не загружать
все плагины сразу в `vimstartuptime`.

Например, если у меня есть плагин `A`, который загружается лениво и плагин `B`,
который требует плагин `A`, то плагин `A` будет загружен как нужно (только
вместе с плагином `B`).

Плагин загружается лениво в 3-х случаях:
    - Если в таблице плагинов `plugins` в `require("lazy").setup(plugins, opts)`
      он указан зависимым от другого плагина.
    - Ленивая загрузка на `event`, `cmd`, `ft`, `keys`.
    - если `config.defauts.lazy == true`


                                          | lazy-lock.json |

Lockfile `lazy-lock.json`
    Этот файл содержит версии плагинов, который установленны через `lazy`.
    Команда `:Lazy restore` обновит плагины до тех версий, которые указаны в
    файле `lazy-lock.json`


                                             | Performance |

Большое внимание было уделено тому, чтобы сделать код (`lazy.core`) максимально
эффективным. Во время запуска `lua` файлы, который исполняются до событий
`VimEnter` or `BufReadPre` компилюруются в байт код и кэшируются.

`VimEnter`
    After doing all the startup stuff, including
    loading vimrc files, executing the "-c cmd"
    arguments, creating all windows and loading
    the buffers in them.

`BufReadPre`
    When starting to edit a new buffer, before reading the file into the buffer.



                                        | Startup Sequence |

`lazy.nvim` does NOT use Neovim packages and even disables plugin loading
completely (`vim.go.loadplugins = false`). It takes over the complete startup
sequence for more flexibility and better performance.

(Neovim Initialization)[https://neovim.io/doc/user/starting.html#initialization]

So we have the next startup sequence:
1. All the plugins' `init()` functions are executed
2. All plugins with `lazy=false` are loaded.
   This includes sourcing `/plugin` and `/ftdetect` files.
   (`/after` will not be sourced yet)
3. All files from `/plugin` and `/ftdetect` directories in your rtp are sourced
   (excluding `/after`)
4. All `/after/plugin` files are sourced (this includes `/after` from plugins)


                                       | Plugins Structure |
