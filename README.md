# emacs_config
從vim轉到emacs的設定紀錄

## 安裝最新版本的emacs
```bash
$ sudo apt-get install libxaw7-dev build-essential libgif-dev libtiff5-dev libjpeg-dev autoconf
$ wget http://ftp.gnu.org/gnu/emacs/emacs-26.1.tar.xz
$ tar -xf emacs-26.1.tar.xz && cd emacs-26.1
$ ./configure --with-x-toolkit=lucid --with-modules
$ make bootstrap
$ sudo make install
```

## 安裝spacemacs預設字體Soure Code Pro
```bash
$ wget https://github.com/adobe-fonts/source-code-pro/archive/2.030R-ro/1.050R-it.tar.gz
$ tar -xf 1.050R-it.tar.gz && cd source-code-pro-2.030R-ro-1.050R-it/OTF
$ sudo mkdir -p /usr/share/fonts/opentype
$ sudo cp ./* /usr/share/fonts/opentype
$ sudo fc-cache -f -v
```

## 安裝spacemacs
```bash
$ rm -rf ~/.emacs.d
$ git clone --recursive https://github.com/syl20bnr/spacemacs ~/.emacs.d
```

## 第一次啟動emacs以安裝相關套件
```bash
# GUI mode
$ emacs

# command line mode
$ emacs -nw
```

## 打開設定檔 .spacemacs 更改預設設定

### 預設啟動的layer
```lisp
dotspacemacs-configuration-layers
'(
  ;; default setting
  ...
  ;; enable or add the following layers
  helm
  auto-completion
  themes-megapack
  )
```

### 預設editing mode
```lisp
dotspacemacs-editing-style 'vim
```

### 打開emacs時視窗最大化 (GUI only)
```lisp
dotspacemacs-maximized-at-startup t
```

### 顯示行數
```lisp
dotspacemacs-line-numbers t
```

### trailing whitespace
```lisp
dotspacemacs-whitespace-cleanup nil

;; 若要自動把trailing whitespace刪除
dotspacemacs-whitespace-cleanup 'trailing
```

### 更改字體或字的大小
```lisp
dotspacemacs-default-font '("Source Code Pro"
                            :size 15
                            :weight normal
                            :width normal
                            :powerline-scale 1.1)
```

### 更改theme
```lisp
dotspacemacs-themes '(zenburn)
```

### 其他設定 (如快捷鍵)皆在 `user-config` 底下
```lisp
;; customized indent function
(defun my-setup-indent (n)
  ;; java/c/c++
  (setq-local c-basic-offset n)
  )

(defun dotspacemacs/user-config ()

  ;; disable tab mode
  (setq-default indent-tabs-mode nil)

  ;; set style
  (setq-default c-default-style '((java-mode . "java")
                                  (other . "allman")))
  ;; set default tab width
  (setq-default tab-width 4)

  ;; customize indent width
  (my-setup-indent 4)

  ;; disable auto-complte of closing bracket
  ;; when I use the following code, I got errors
  ;; after reload .spacemacs
  ;; the alternative way is disabling smartparens directly
  ;; dotspacemacs-excluded-packages '(smartparens)
  (eval-after-load 'smartparens
    '(progn
      (sp-pair "(" nil :actions :rem)
      (sp-pair "[" nil :actions :rem)
      (sp-pair "{" nil :actions :rem)
      (sp-pair "'" nil :actions :rem)
      (sp-pair "\"" nil :actions :rem)
      ))

  ;; enable highlight on parenthesis
  (progn
    (show-paren-mode 1)
    (setq show-paren-style 'parenthesis)
    )
  )

  ;; enable clipboard and disable primary
  (setq x-select-enable-clipboard t)
  (setq x-select-enable-primary nil)

  ;; helper function to utilize clipboard
  (defun copy-to-clipboard ()
    "Copies selection to x-clipboard."
    (interactive)
    (if (display-graphic-p)
	(progn
          (message "Yanked region to x-clipboard!")
          (call-interactively 'clipboard-kill-ring-save)
          )
      (if (region-active-p)
          (progn
            (shell-command-on-region (region-beginning) (region-end) "xsel -i -b")
            (message "Yanked region to clipboard!")
            (deactivate-mark))
	(message "No region active; can't yank to clipboard!"))
      )
    )
  (defun paste-from-clipboard ()
    "Pastes from x-clipboard."
    (interactive)
    (if (display-graphic-p)
        (progn
          (clipboard-yank)
          (message "graphics active")
          )
      (insert (shell-command-to-string "xsel -o -b"))
      )
    )

  ;; set shortcut to copy to/paste from clipboard
  (evil-leader/set-key "o p" 'paste-from-clipboard)
  (evil-leader/set-key "o y" 'copy-to-clipboard)

  ;; proper setting for shell mode
  (autoload 'ansi-color-for-comint-mode-on "ansi-color" nil t)
  (add-hook 'shell-mode-hook 'ansi-color-for-comint-mode-on t)

  ;; set proper color value for region
  (set-face-attribute 'region nil :background "#666")

  ;; disable lazy-highlight for search result
  (setq lazy-highlight-cleanup nil)

  ;; set proper color value for search result
  (set-face-attribute 'lazy-highlight nil :background "#ffff00" :foreground "#000000")

  ;; set proper color value for trailing whitespace
  (set-face-attribute 'trailing-whitespace nil :background "#ff0000" :foreground "#ffffff")

  ;; set proper color value for matched parentheses
  (set-face-attribute 'show-paren-match nil :background "#5ac1e1" :foreground "#ffffff")

  ;; draw underline to current cursor position
  (unless (display-graphic-p)
      (progn
        (set-face-background 'hl-line nil)
        (set-face-foreground 'hl-line nil)
        (set-face-underline  'hl-line t)
        )
    )

  ;; add space between line number and text content
  (unless (display-graphic-p)
    (setq linum-format (concat linum-format " ")))

  ;; insert a tab when we hit TAB in insert state
  (define-key evil-insert-state-map (kbd "TAB") 'tab-to-tab-stop)

  ;; delete a char when we hit in insert state
  (setq-default c-backspace-function 'delete-backward-char)
  (define-key evil-insert-state-map [backspace] 'delete-backward-char)
  )
```

## 重新啟動emacs以安裝相關套件
```bash
# GUI mode
$ emacs

# command line mode
$ emacs -nw
```

## 安裝c-c++ layer
Dependence: clang, llvm, gcc, cmake

```bash
$ sudo apt-get install clang-6.0 llvm-6.0 llvm-6.0-dev libclang-6.0-dev cmake
```

TODO!

# emacs指令操作
TODO!

## 離開emacs
```lisp
C-x C-c
```

## 開啟/重新載入.spacemacs
```lisp
;; 開啟.spacemacs
SPC f e d

;; 重新載入.spacemacs
;; 不知為何，有時重載無效，還是需要重啟emacs
SPC f e R
```

## helm
```lisp
;; 打開helm
C-x f

;; 游標往下
C-j

;; 游標往上
C-k

;; 進入資料夾, 或打開檔案
;; 打開檔案也可以直接按enter (RET)
C-l

;; 往上一層目錄
C-h

;; 可以直接輸入路徑來filter檔案, 按TAB可補全
;; 按ESC離開
;; PS: 用helm就不需要neotree了
;; 且有發現在開啟neotree時使用helm, 會讓C-l失效
```

## Buffer related

```lisp
;; 和vim相同的開啟檔案方法
:e <file path>

;; open buffer list
SPC b b

;; 在buffer list中, 游標往上往下
C-k ;; up
C-j ;; down

;; 選擇buffer
RET

;; 在buffer list中可以輸入關鍵字做filter

;; 在buffer list中刪除buffer
C-c d

;; 在buffer list中rename buffer
F9

;; 在normal mode下switch至last buffer
SPC TAB

;; 在normal mode下switch至下一個buffer
SPC b n

;; 在normal mode下switch至上一個buffer
SPC b p

;; 在normal mode下刪除現在的focused buffer
SPC b d

;; 在normal mode下刪除除了focused buffer以外的所有buffer
SPC b m
```

## execute command in emacs
```lisp
;; 執行指令並顯示在一個新的buffer上
M-! <shell command>
M-x shell RET <shell command>

;; run a shell in a new buffer
M-x term

;; run eshell
M-x eshell

;; 在shell中, 按ESC回到normal mode, 可進行複製, 貼上等動作
;; 按i進入insert mode, 輸入指令

;; 刪除一個word
M-backspace

;; cursor往前一個word
M-b

;; cursor往後一個word
M-f
```
## copy to/paste from clipboard
```lisp
;; 此指令需要先設定user-config，請確認相關自定義function都有

;; copy to clipboard
;; 用visual mode或滑鼠選取想要複製的區域後
SPC o y

;; paste from clipboard
;; 在normal mode下
SPC o p
```

# 已知問題
## normal mode和insert mode之間切換很慢
這是因為tmux的設定關係，將以下設定加入`.tmux.conf`中
```bash
set -s escape-time 0
```

## emacs terminal mode的顏色很奇怪
可能和terminal的color設定有關，在`.bashrc`或`.zshrc`等dotfile中加入
```bash
export TERM=xterm-256color
```

## emacs shell mode中下指令會有色碼出現
和shell設定有關，編輯`dumb-emacs-ansi.ti`
```bash
dumb-emacs-ansi|Emacs dumb terminal with ANSI color codes,
    am,
    colors#8, it#8, ncv#13, pairs#64,
    bold=\E[1m, cud1=^J, ht=^I, ind=^J, op=\E[39;49m,
    ritm=\E[23m, rmul=\E[24m, setab=\E[4%p1%dm,
    setaf=\E[3%p1%dm, sgr0=\E[m, sitm=\E[3m, smul=\E[4m, 
```

執行以下指令編譯
```bash
$ tic dumb-emacs-ansi.ti
```

接著在`.bashrc`或`.zshrc`中加入
```bash
if [ "$INSIDE_EMACS" ]; then
    export TERM=dumb-emacs-ansi COLORTERM=1
fi
```

## 開啟速度很慢
可以用emacs的client/server架構，待確認

## search時是case insensitive
似乎沒有類似vim中的`set ic`和`set noic`的command
在搜尋時按`M-c`可以選擇是否要case insensitive，但有時沒有效果

## 無法根據不同副檔名設定tab width和indent size
待確認

## C/C++的code trace tool
還未嘗試，待確認
