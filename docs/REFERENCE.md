# Документация по публичным API, функциям и компонентам

## Обзор
Этот репозиторий содержит пользовательскую конфигурацию Vim: настройки (`.vimrc`), цветовые схемы (`colors/`), а также менеджер плагинов (`autoload/plug.vim`). Данный документ описывает доступные пользователю публичные интерфейсы: команды, функции, маппинги клавиш, автокоманды, глобальные переменные и параметры, а также список установленных плагинов. В конце добавлены примеры использования.

---

## Публичные функции

- BuildYCM(info)
  - Назначение: хук сборки для плагина YouCompleteMe при установке/обновлении.
  - Вход: `info` — словарь с полями `name`, `status` (`installed`/`updated`/`unchanged`), `force` (флаг `!`).
  - Поведение: при `installed` или `force` запускает установщик YCM с `--clang-completer --gocode-completer`.
  - Пример:
    ```vim
    Plug 'Valloric/YouCompleteMe', {'do': function('BuildYCM')}
    ```

Примечание: остальные функции, найденные в `autoload/plug.vim`, являются внутренними (префикс `s:`) и не предназначены для прямого вызова пользователем. Публичная поверхность менеджера плагинов — это его команды (см. ниже).

---

## Публичные команды

- Plug
  - Описание: регистрация плагина для установки через vim-plug.
  - Использование:
    ```vim
    Plug 'owner/repo'
    Plug 'owner/repo', { 'on': 'CommandOrMapping' }
    ```

- PlugInstall[!]
  - Описание: установка зарегистрированных плагинов. `!` принудительно переустанавливает.
  - Использование: `:PlugInstall`, `:PlugInstall! repo1 repo2`

- PlugUpdate[!]
  - Описание: обновление плагинов. `!` — принудительное.
  - Использование: `:PlugUpdate`, `:PlugUpdate!`

- PlugClean[!]
  - Описание: удаление неиспользуемых плагинов. `!` — без подтверждения.
  - Использование: `:PlugClean`, `:PlugClean!`

- PlugUpgrade
  - Описание: обновление самого vim-plug.
  - Использование: `:PlugUpgrade`

- PlugStatus
  - Описание: показать статус плагинов.
  - Использование: `:PlugStatus`

- PlugDiff
  - Описание: показать изменения (diff) плагинов.
  - Использование: `:PlugDiff`

- PlugSnapshot [file]
  - Описание: сохранить снапшот версий плагинов.
  - Использование: `:PlugSnapshot ~/.vim/plug-snapshot.vim`

- YouCompleteMe (событие User)
  - Автокоманда: `autocmd! User YouCompleteMe call youcompleteme#Enable()` — включает YCM при возникновении события.

---

## Маппинги клавиш

- Normal: <F10>
  - Действие: переключение режима вставки без форматирования (paste) и отображение текущего состояния (`paste?`).
  - Команда: `nnoremap <F10> :set invpaste paste?<CR>`
  - Связанная опция: `set pastetoggle=<F10>` — альтернативный встроенный тоггл.

- Normal: <Leader>m
  - Действие: сохранить файл и скомпилировать текущий `.less` в `.css` через `lessc`.
  - Команда: `nnoremap <Leader>m :w <BAR> !lessc % > %:t:r.css<CR><space>`
  - Требования: установленный `lessc` (npm пакет `less`).

Лидер-клавиша: `,` (задана через `let mapleader = ","`).

---

## Автокоманды и группы

- filetypedetect
  - Правила:
    - `*.csv, *.dat` → `setfiletype csv`
    - `*.less` → `set filetype=less`
    - `*.json` → `set filetype=json`

- json_autocmd (для `FileType json`)
  - `set autoindent`
  - `set formatoptions=tcq2l`
  - `set textwidth=78 shiftwidth=2`
  - `set softtabstop=2 tabstop=8`
  - `set expandtab`
  - `set foldmethod=syntax`

- Прочие одиночные автокоманды
  - `autocmd FileType html :setlocal shiftwidth=2 ts=2 softtabstop=2`
  - `autocmd Filetype javascript :setlocal shiftwidth=2 ts=2 softtabstop=2`
  - `au BufReadPost *.hbs set syntax=html`

---

## Глобальные переменные (пользовательские/плагинные)

- `g:SESSION_DIR` — директория сессий: `$HOME/.cache/vim/sessions`
- `g:username`, `g:email` — из окружения `$USERNAME`, `$EMAIL`
- `g:template_dir` — каталог шаблонов: `$HOME/.vim/templates/`
- `g:ycm_global_ycm_extra_conf` — путь к конфигу YCM
- `g:Powerline_symbols` — стиль символов airline/powerline: `fancy`
- `g:NERDSpaceDelims` — включить пробелы в комментариях для nerdcommenter: `1`
- `g:pymode_rope_lookup_project` — выключен: `0`
- `g:pymode_rope` — выключен: `0`
- `g:pymode_breakpoint_cmd` — строка для брейкпоинта: `import ipdb; ipdb.set_trace()  # XXX BREAKPOINT`
- Syntastic:
  - `g:syntastic_python_checkers = []`
  - `g:syntastic_javascript_checkers = ['eslint']`
  - `g:syntastic_always_populate_loc_list = 1`
  - `g:syntastic_auto_loc_list = 1`
  - `g:syntastic_check_on_open = 1`
  - `g:syntastic_check_on_wq = 0`
  - `g:syntastic_loc_list_height = 3`
- Прочее:
  - `g:jsx_ext_required = 0`
  - `g:changelog_username = "$USERNAME <$EMAIL>"`

---

## Ключевые опции Vim (из этого конфигурационного файла)

- Режимы и UI: `nocompatible`, `syntax on`, `filetype plugin indent on`, `title`, `visualbell`, `linebreak`, `showcmd`, `cmdheight=2`, `statusline += SyntasticStatuslineFlag()`
- Буферы/файлы: `hidden`, `autoread`, `autowrite`
- Прокрутка и курсор: `scrolloff=4`, `sidescroll=4`, `sidescrolloff=10`, `whichwrap=b,s,<,>,[,],l,h`
- Дополнение: `completeopt=menu,preview`, `infercase`
- Табуляция: `autoindent`, `smartindent`, `expandtab`, `smarttab`, `shiftwidth=4`, `softtabstop=4`, `shiftround`
- История: `history=1000`, `viminfo+=h`, `ssop-=blank`, `ssop-=options`
- Поиск: `hlsearch`, `ignorecase`, `smartcase`, `incsearch`
- Скобки: `showmatch`, `matchpairs+=<:>`
- Локализация: `langmenu=none`, `keymap=russian-jcukenwin`, `iminsert=0`, `imsearch=-1`, `spelllang=en,ru`
- Кодировки: `encoding=utf-8`, `fileencodings=utf-8,cp1251,koi8-r,cp866`, `termencoding=utf-8`
- Wildmenu: `wildmenu`, `wildcharm=<TAB>`, `wildignore=*.pyc`
- Undo: `undofile` (если доступно), `undodir=/tmp/`
- Сворачивание: `foldmethod=marker`, `foldlevel=999`
- Буфер обмена: `clipboard+=unnamed` (если есть `unnamedplus`)
- Терминал и цвета: `t_Co=256`, `colo wombat256mod`
- Редактирование: `backspace=indent,eol,start`, `virtualedit=all`, `confirm`, `numberwidth=1`, `pastetoggle=<F10>`
- Безопасность/проект: `exrc`, `secure`

---

## Плагины (через vim-plug)

Указаны в `.vimrc` в блоке между `call plug#begin(...)` и `call plug#end()`:

- `kien/ctrlp.vim` — быстрый файловый поиск
- `bling/vim-airline` — статусная линия
- `scrooloose/nerdtree` — файловый браузер (ленивая загрузка по `NERDTreeToggle`)
- `scrooloose/nerdcommenter` — комментирование
- `kana/vim-fakeclip` — буфер обмена
- `klen/python-mode` — Python IDE-функции
- `tpope/vim-fugitive` — Git интеграция
- `tpope/vim-surround` — работа с окружениями
- `mattn/emmet-vim` — Emmet для HTML/CSS
- `c.vim` — инструменты C
- `derekwyatt/vim-scala` — поддержка Scala
- `rking/ag.vim` — интеграция с ag (The Silver Searcher)
- `majutsushi/tagbar` — боковая панель тегов
- `Rykka/riv.vim` — reStructuredText
- `hallison/vim-markdown` — Markdown
- `vim-scripts/vimwiki` — Wiki
- `itchyny/calendar.vim` — календарь
- `vim-scripts/TaskList.vim` — список задач
- `jceb/vim-orgmode` — Org-mode
- `Valloric/YouCompleteMe` — автодополнение (со сборкой через `BuildYCM`)
- `rdnetto/YCM-Generator` — генератор конфигов для YCM
- `csv.vim` — работа с CSV
- `Gundo` — визуальная история undo
- `scrooloose/syntastic` — синтаксическая проверка
- `LargeFile` — ускорение для больших файлов
- `L9` — вспомогательный плагин
- `mattn/webapi-vim` — Web API
- `mattn/vdbi-vim` — база данных
- `pangloss/vim-javascript` — JavaScript
- `mxw/vim-jsx` — JSX/React
- `nathanaelkane/vim-indent-guides` — визуализация отступов
- `othree/html5.vim` — HTML5
- `kchmck/vim-coffee-script` — CoffeeScript
- `gorodinskiy/vim-coloresque` — подсветка цветов
- `groenewege/vim-less` — LESS
- `nginx.vim` — конфиги Nginx
- `JuliaLang/julia-vim` — поддержка Julia
- `bufexplorer.zip` — переключение буферов
- `kana/vim-arpeggio` — последовательности клавиш
- `chase/vim-ansible-yaml` — Ansible/YAML
- `FredKSchott/CoVim` — совместное редактирование
- `dbext.vim` — работа с DB
- `elzr/vim-json` — улучшенный JSON

Примечание: некоторые плагины требуют внешних зависимостей (см. README).

---

## Цветовые схемы

- `colors/wombat256.vim`
- `colors/wombat256mod.vim`

Активная схема: `colo wombat256mod`.

---

## Примеры использования

- Установка/обновление плагинов
  ```vim
  :PlugInstall        " установить плагины
  :PlugUpdate         " обновить плагины
  :PlugClean!         " удалить неиспользуемые без подтверждения
  :PlugStatus         " показать статус
  ```

- Добавление плагина в конфиг
  ```vim
  call plug#begin('~/.vim/plugged')
  Plug 'tpope/vim-commentary'
  call plug#end()
  ```

- Сборка YouCompleteMe вручную (из README)
  ```bash
  ~/.vim/plugged/YouCompleteMe/install.sh --clang-completer --gocode-completer
  ```

- Компиляция LESS в CSS из текущего файла
  ```vim
  <Leader>m   " запустить lessc для текущего буфера
  ```

- Быстрая настройка для JSON
  ```vim
  autocmd FileType json setlocal ts=2 sw=2 sts=2 expandtab
  ```

---

## Требования и внешние зависимости

См. `README.md`:
- ag (The Silver Searcher), ctags, clang/LLVM, node/npm утилиты (`eslint`, `less`), Perl модули (`DBI`, `Plack`, `JSON`).
- Для YCM — компиляция с соответствующими флагами.

---

## Версии и совместимость

- Конфигурация использует `vim-plug` (см. `autoload/plug.vim`).
- Некоторые опции включаются при наличии соответствующих возможностей Vim (`has('persistent_undo')`, `has('unnamedplus')`).

---

## Лицензия

См. лицензии самих плагинов; данный `.vimrc` и документация под лицензией репозитория (если указана).

---

## Команды сборки (Makefile)

- `make install`
  - Создаёт симлинк `~/.vimrc` на текущий `.vimrc` и запускает `vim +PlugInstall` для установки плагинов.
- `make clean`
  - Удаляет `~/.vimrc`.

Пример:
```bash
make install
make clean
```