# 真正的 CS 人，从命令行开始

这部分的教程确实很多，比如前面提到的[实用技能拾遗](https://www.bilibili.com/video/BV1ry4y1A7qo)和 [Missing Semester](https://missing.csail.mit.edu/2020/course-shell/).

## 什么是 shell，什么是 Terminal，什么是 CLI

> Nearly all platforms you can get your hands on have a shell in one form or another, and many of them have several shells for you to choose from. While they may vary in the details, at their core they are all roughly the same: they allow you to run programs, give them input, and inspect their output in a semi-structured way.
>
> -- The Missing Semester

简而言之，shell 就是接收用户键入的命令并解释，将其传递给操作系统执行的程序，而 Terminal 将这一过程图形化（提供了一个窗口），允许用户键入文本、显示输入输出. 这种"用户通过键入文本命令来操作计算机"的方式就叫做 CLI (Command Line Interface).

从而我们要使用命令行，就需要抓住三点：

- 使用什么Terminal;
- 使用什么shell;
- 如何使用.

## Terminal 的选择

> 参考[实用技能拾遗辅学讲义](https://slides.tonycrane.cc/PracticalSkillsTutorial/2023-fall-ckc/lec1/#/2/3/1){: target="_blank"}，因为本人目前只有 Windows 设备（

- **Windows**：毫无疑问，当前只有 Windows Terminal 这一个最优选择.
- Linux：Gnome Terminal、Konsole、LXTerminal 等.
- macOS：Terminal.app、iTerm2（推荐）等.
- 跨平台：
    - Alacritty：基于 Rust 开发.
    - Warp：基于 Rust 开发.
    - Hyper：基于 Electron 开发.
    - ...

## Shell 的选择

- **Windows**：PowerShell（推荐）、cmd.exe（更快但是原生的命令行工具较少，也就是功能不够强大）.
- **\*nix**：
    - 经典 bash(Bourne Again SHell)，很多 Linux 发行版的默认 shell.
    - zsh(Z Shell)，功能强大且高度自定义，现在 macOS 的默认 shell，配合 oh-my-zsh 使用体验更佳.
    - fish(Friendly Interactive SHell)，开箱即用、全平台支持.
    - ...

> 关于 zsh 与 oh-my-zsh 的配置，[辅学讲义](https://slides.tonycrane.cc/PracticalSkillsTutorial/2023-fall-ckc/lec1/#/2/8){: target="_blank"}中有较详细介绍.

## 如何使用--基础 Shell 命令

??? note "有关 powershell/cmd"
    powershell 支持部分 bash 的原生命令，但是功能性还是不够多；cmd 支持的就更少了. 因此 Windows 用户推荐使用 Windows Subsystem for Linux (WSL) 转向使用 Unix Shell.

    不过如果是 Windows 系统，掌握一点 powershell/cmd 命令还是有必要的，毕竟它们算是 Windows 的原生 shell 了. 至少在 Windows 电脑上写一些便捷操作的批处理脚本还是用 powershell/cmd 命令比较方便. 

我们会介绍一些基础的 bash 命令以及命令思想.

### 最基础的：执行命令时各种信息是什么意思

比如在启动 bash 时可以看到类似于如下的界面：

```bash
starringtide@os-cw-pc:~$
```

这实际上就是所谓的"命令提示符"(prompt)，它告诉我们当前 shell 的一些信息：

- `starringtide`：当前用户的用户名.
- `os-cw-pc`：当前计算机的主机名(hostname).
- `~`：当前所在的路径(path). `~` 代表用户的 home 目录. 对于普通用户（如 starringtide），home 目录一般在 `/home/username` 下，而对于 root 用户，home 目录一般在 `/root` 下.
    - 一个目录/文件夹就是一级路径，不同级路径之间用斜杠`/`(Unix)或反斜杠`\`(Windows)隔开. 一般除了在 Windows 上进行文件访问（如使用 Windows 的 shell 或者使用文件管理器）以外 Windows 的路径分隔往往需要使用双反斜杠`\\`，
    - 在 shell 操作中，路径思想非常重要，自己当前在什么路径，要去到什么路径，目标文件的操作涉及到哪些路径等等，这就需要操作电脑的人对电脑的文件目录有较为清晰的认识.
- `$`：提示符的结束符. 一般来说，普通用户是 `$`，而 root 用户是 `#`. 普通用户在访问系统文件时会受到权限限制，如果需要访问需要在本来的访问指令前加上 `sudo` 来提权，而 root 用户则不需要.

### 常用命令

- 路径相关：cd, pwd
    - `cd [path]`：change directory，切换路径. `cd` 后面跟上想要切换到的路径即可. 对于 bash、zsh 等，如果不跟任何参数，默认切换到 home 目录.
        - 切换可以是相对路径，也可以是绝对路径. 绝对路径是从根目录(`/`)开始的完整路径，而相对路径则是从当前目录开始的路径，
        - `..` 代表上一级路径，`.` 代表当前路径.
        - 例如非 root 用户从 `~` 进入 `/usr/local/bin`，可以使用绝对路径 `cd /usr/local/bin`，也可以使用相对路径 `cd ../../usr/local/bin`.（注意到 home 目录在 `/home/username` 下）
        - `cd -`：切换到上一次所在的路径.
    - `pwd`：print working directory，显示当前绝对路径.
- 文件/文件夹相关：ls, mkdir, touch, rm, cp, mv
    - `ls [path]`：list，列出指定路径下的文件/文件夹. 如果不指定路径，则列出当前路径下的文件/文件夹.
        - 常用参数：`-a`（all，显示所有文件，包括隐藏文件），`-l`（long，显示详细信息），`-h`（human-readable，以人类可读的方式显示文件大小）等. 可以 `-al` 这样互相组合.
        - powershell 中对应命令为 `Get-ChildItem` 或其别名 `ls`/`dir`，仅支持 `-h` 参数（且含义发生改变，为只展示隐藏文件），但是 list 时实际上会显示详细信息：
```text
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          2025/5/2     10:24                .github
d-----        2025/11/21     21:43                docs
-a----         2025/2/22     16:52             41 .gitignore
-a----         2025/4/30     11:33           1090 LICENSE
-a----        2025/11/22     15:37           2679 mkdocs.yml
-a----        2025/11/21     22:13            193 README.md
-a----        2025/11/21     22:16            128 requirements.txt
```
    - `mkdir [dir_name]`：make directory，创建一个新的文件夹，支持跨目录创建.
    - `touch [file_name]`：创建一个新的空文件（如果不存在），或者更新已有文件的访问和修改时间（如果存在）.
    - `rm [file_name]`：remove，删除指定的文件.
        - 常用参数：`-r`（recursive，递归删除，用于删除文件夹及其内容），`-f`（force，强制删除，不提示确认）等. 同样可以组合.
    - `cp [source] [destination]`：copy，复制文件或文件夹.
        - 常用参数：`-r`（recursive，递归复制，用于复制文件夹及其内容）等.
    - `mv [source] [destination]`：move，移动或重命名文件或文件夹.
        - 例：在当前目录下创建了两个目录 `dir1` 和 `dir2`，运行命令 `mv dir1 dir_new` 会将 `dir1` 重命名为 `dir_new`；再运行命令 `mv dir_new dir2/` 会将 `dir_new` 移动到 `dir2` 目录下.
- 查看文件内容相关：cat, less, head, tail
    - `cat [file_name]`：concatenate and display，显示文件内容. `-n` 参数可以显示行号.
    - `less [file_name]`：分页显示文件内容，适用于查看较长的文件. 空格翻页，回车下一行，q 退出.
    - `head [file_name]`：显示文件的前几行，默认显示前 10 行，可以使用 `-n` 参数指定行数.
    - `tail [file_name]`：显示文件的后几行，默认显示后 10 行，可以使用 `-n` 参数指定行数.
- 其他常用命令
    - `man [command]`：manual，查看命令的使用手册和帮助信息. 最权威的命令使用说明.
        - 不妨试试 `man man` 呢（
    - `echo [text]`：在终端显示一段文本.
    - `clear`：清屏，清除终端上的所有内容.
    - `history`：显示历史命令记录.
    - `exit`：退出当前 shell 会话.
    - `whereis/which/whence`：查找命令的位置.
    - `chmod`：change mode，修改文件或文件夹的权限.
    - `ps`：显示当前运行的进程信息.
    - `kill [pid]`：终止指定进程，`pid` 是进程的 ID，可以通过 `ps` 命令查看.
    - `grep`：搜索文件内容（常配合重定向 / 管道使用）.
    - `diff`：比较文件 / 目录内容.
    - `curl`：发送 HTTP 请求.
    - `wget`：下载文件.

### 环境变量

记录了系统信息的变量，可供所有程序读取. 在 bash、zsh 等 shell 中，可以通过 `echo $VAR_NAME` 来查看环境变量 `VAR_NAME` 的值，通过 `env` 来列出所有的环境变量.

其中 `PATH` 是最重要的环境变量之一（但<u>不是全部</u>），它记录了 shell 在查找可执行命令时所搜索的路径列表. 当你在 shell 中输入一个命令时，shell 会按照 `PATH` 中列出的路径顺序查找该命令对应的可执行文件. 如果找到了，该命令就能正常执行，否则就会返回错误："command not found".

!!! Tip
    这也就是为什么在执行当前目录下的可执行文件时（如 `a.out`）执行的应该是 `./a.out` 而不是 `a.out`：当前目录 `.` 通常不在 `PATH` 变量中.

临时添加环境变量可以简单的用 `export VAR_NAME=value`，只在当前 shell 中生效. 永久添加则需要修改 shell 的配置文件（bash 为 `~/.bashrc`，zsh 为 `~/.zshrc` 等），添加一行 `export VAR_NAME=value` 即可.

`unset VAR_NAME` 可以删除环境变量.

!!! Warning
    为了不影响已有的变量，特别是 `PATH` 变量，应当使用 `export PATH=$PATH:/new/path` 这样的形式，保留已有部分的同时将的部分添加进去，`:` 充当分隔符. 在向配置文件中添加条目时尤其要注意，不然可能会导致系统无法找到已有的命令，那样的话不就炸了（


### 配置文件

本质上是在加载 shell 时执行的一些脚本文件，最常用的就是 `~/.bashrc` (bash) 和 `~/.zshrc` (zsh). 如果默认装的是 bash 后来又安装了 zsh 等别的 shell 这种情况，就更推荐在各自的 `.*rc` 文件中进行配置. 

设置了 shell 的配置文件后，重开新 shell 才会有效果. 或者你可以选择 `source` 该配置文件来立刻加载.（实际上就是执行了一次该文件，重开 shell 也是一样的道理，因为在启动 shell 时会自动执行配置文件）

因为配置文件实际上仍然是脚本文件，因此实际上应该在了解了如何编写 shell scripts 之后再来看配置文件会好一些. 关于这部分我们会在 [shell 脚本](../cli_script_tool/script.md)中介绍，但在那之前，我们可以"预了解"一下 shell 脚本做了什么：

> In many scenarios you will want to <u>perform a series of commands and make use of control flow expressions like conditionals or loops</u>. Shell scripts are the next step in complexity. Most shells have their own scripting language with variables, control flow and its own syntax. What makes shell scripting different from other scripting programming languages is that it is optimized for performing shell-related tasks. Thus, creating command pipelines, saving results into files, and reading from standard input are primitives in shell scripting, which makes it easier to use than general purpose scripting languages.
>
> -- The Missing Semester

所以 shell 脚本其实就是运行了一连串指令，并且可以使用控制流表达式（比如条件、循环这些基础的编程概念）来实现更复杂的逻辑. 你可能并不了解后者，但是运行一连串指令想必很容易理解 —— 不就是运行一条指令 \* n 嘛. 因此尽管你可能并不知道 shell 脚本语言的任何语法，你还是可以写一些简单的脚本函数放进你的配置文件中，从而帮助你完成一些需要多条指令（或者一条很长的但是大部分参数固定的指令）的任务.

> 为什么我会提到这个 —— <s>因为我就是这么做的</s>.

举个简单的例子，在 WSL 中你的 home 目录是在 `/home/username` 下，但是如果需要访问 Windows 文件的话就需要去到 `/mnt/c/Users/username` 或者 `/mnt/d/xxx` 这样的路径下. 如果你需要经常的访问 Windows 的某个路径下的文件夹（比如说 `D:\codes`），每次都敲完整的路径会非常麻烦，因此你可以在配置文件中添加如下函数：

```bash
function cdc(){
    cd /mnt/c/Users/username/
}
function cdd(){
    cd /mnt/d/codes/
}
```

再比如打开配置文件与加载配置文件这件事，需要执行 `vim ~/.bashrc` 和 `source ~/.bashrc` 两条指令（`vim` 也可以换成其他编辑器）. 如果你经常需要修改配置文件的话，可以添加如下函数：

```bash
function rc(){
    vim ~/.bashrc
}
function ss(){
    source ~/.bashrc
}
```

如果你需要经常打开某个目录作为工程目录，可能就需要频繁的切换到该目录并启用编辑器，同时可能还需要启动一些虚拟环境配置什么的，这种情况下你也可以写一个函数来完成这些操作：

```bash
function proj(){
    cd /path/to/your/project
    source venv/bin/activate
    code .
}
```

这样的话就可以把一些较长的或较为常用的指令用简单的函数名代替，从而提高效率. <s>一个小白的、但是确实能提高效率的配置文件就暂时成型了</s>.

### 重定向

刚刚提到，shell 是接收用户输入、交给计算机处理并进行输出的程序，因此“输入流”和“输出流”是 shell 的重要概念：当 shell 尝试读取输入时，它从输入流读取，当 shell 打印某物时，再打印到输出流（除了标准输入流 stdin、标准输出流 stdout 外还有标准错误流 stderr）. 一般这些流连接的都是终端和计算机，但是可以通过重定向改变这个连接.

最简单的重定向是通过 `<` 和 `>`. 简而言之，`> file` 将 stdout 重定向到文件，`< file` 将文件重定向到 stdin，`2> file` 将 stderr 重定向到文件.

`>>` 表示追加重定向，即不覆盖原文件，而是追加到文件末尾. `&> file` 将 stdout 和 stderr 都重定向到文件.

作为练习，尝试使用重定向将你们平时编程生成的 C 语言可执行文件的输入输出从在终端中进行改为从文件中读取输入并将输出写入文件中. 你会发现对于大型的输入输出，这么做是十分方便且必要的.

### 管道

管道(pipe)可以将一个命令的输出作为另一个命令的输入，具体来说，通过操作符 `|`，左侧命令的 stdout 会和右侧命令的 stdin 相连，进而可以通过管道将多个命令连接起来，通过一行命令实现相对复杂的操作.

常见用法：

- `some cmd | tail -n lines`：只输出最后 lines 行.
- `some cmd | less`：分页输出.
- `some cmd | grep pattern`：在输出中查找匹配 pattern 的行.
- 与 `cut` / `sort` / `uniq` / `awk` 等命令搭配，处理文本数据.
- ...

作为一个简单的练习，尝试找到在本仓库的文件中"命令行"一词出现的所有位置.（mkdocs-material 的搜索做不到这一点，因为对中文搜索的支持做得较差）