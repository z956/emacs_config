# spacemacs_config
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

  ;; customize indent width
  (my-setup-indent 4)

  ;; shortcut of pasting from x-clipboard to emacs
  (global-set-key (kbd "C-S-v") 'x-clipboard-yank)

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

;; 在normal mode下switch buffer
SPC TAB

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
```

# 已知問題
## 無法在insert mode (emacs叫做insert state)下按TAB來輸入`'\t'`
可以在`user-config`下加入設定來修改, 但不確定有什麼副作用
```lisp
(define-key evil-insert-state-map (kbd "TAB") 'tab-to-tab-stop)
```

## 在insert mode下按baskspace刪除tab時, tab會被轉換成space, 並且只能逐一刪除space
更改`dotspacemacs/init`下的`dotspacemacs-whitespace-cleanup`設定

```lisp
dotspacemacs-whitespace-cleanup 'hungry
```

<p>但設定為hungry mode後，刪除時是會將所有trailing whiltespace, tab給刪除</p>
<p>和vim底下的一個tab或space刪除是不同的</p>

## tab相關
<p>上兩個tab相關問題...現在找不到和vim類似的設定<p>

我目前用快捷鍵`C-t`來insert tab，用`C-d`刪除tab

## 和clipboard之間的copy/paste
<p>雖然可以用x-clipboard-yank來做copy/paste, 但有些情況下無法使用</p>

例如在normal mode下按`/`搜尋關鍵字時, 無法貼上關鍵字

## normal mode和insert mode之間切換很慢
<p>網路有人說若用tmux開啟command line mode的emacs，mode之間切換會很慢</p>
<p>目前暫時用GUI版本</p>

## 開啟速度很慢
可以用emacs的client/server架構，待確認

## search時是case insensitive
待確認，應能找到和vim的`set ic`和`set noic`相同的command

## 無法根據不同副檔名設定tab width和indent size
待確認

## C/C++的code trace tool
還未嘗試，待確認
