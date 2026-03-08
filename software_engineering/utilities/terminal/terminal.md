# Terminal Quick Reference (Core, Common Syntax) — Bash/Zsh

**Audience**: devs who just want the stuff they use constantly.

**Notes**:
- "command [options] [args]" is the general shape.
- Quotes matter. Use single quotes to prevent expansion.
- Use TAB for completion, ↑ for history.

## Table of Contents

- [0. Navigation + Listing](#0-navigation--listing)
- [1. Files & Folders (create/copy/move/delete)](#1-files--folders-createcopymovedelete)
- [2. Viewing / Paging / Searching Text](#2-viewing--paging--searching-text)
- [3. Globs & Brace Expansion (fast selection)](#3-globs--brace-expansion-fast-selection)
- [4. Pipes, Redirects, and Here-Strings](#4-pipes-redirects-and-here-strings)
- [5. Command Chaining & Exit Codes](#5-command-chaining--exit-codes)
- [6. Variables, Quotes, and Substitution (Bash basics)](#6-variables-quotes-and-substitution-bash-basics)

---

## 0. Navigation + Listing
- `pwd` - print current directory
- `ls` - list files
- `ls -la` - long list + hidden files
- `ls -lh` - human sizes
- `cd /path/to/dir` - change directory
- `cd ..` - go up one
- `cd ~` - home
- `cd -` - previous directory
- `tree -L 2` - directory tree (if installed)

## 1. Files & Folders (create/copy/move/delete)
- `touch file.txt` - create empty file / update timestamp
- `mkdir newdir` - create folder
- `mkdir -p a/b/c` - create nested folders
- `cp src.txt dst.txt` - copy file
- `cp -r dir1 dir2` - copy directory recursively
- `mv oldname newname` - move/rename
- `rm file.txt` - delete file (no undo)
- `rm -r dir` - delete directory recursively (danger)
- `rm -rf dir` - force delete (very dangerous)
- `ln -s target linkname` - symlink (shortcut)
- `rm -i file.txt` - confirm before delete
- `mv -i a b` - confirm before overwrite

## 2. Viewing / Paging / Searching Text
- `cat file.txt` - print file
- `less file.txt` - scroll (q to quit)
- `head -n 20 file.txt` - first 20 lines
- `tail -n 20 file.txt` - last 20 lines
- `tail -f app.log` - follow log output live
- `wc -l file.txt` - line count
- `grep "ERROR" file.txt` - find matches (basic)
- `grep -n "ERROR" file.txt` - include line numbers
- `grep -R "TODO" .` - recursive search from current dir
- `grep -Ri "token" .` - recursive + case-insensitive
- `rg "pattern" .` - ripgrep (faster, if installed)

## 3. Globs & Brace Expansion (fast selection)
- `ls *.py` - match all .py files
- `ls **/*.py` - recursive glob (zsh; bash needs shopt -s globstar)
- `rm *.tmp` - delete all .tmp in current folder
- `cp file.{bak,txt}` - expands to cp file.bak file.txt
- `mkdir -p src/{api,db,utils}` - create multiple dirs

## 4. Pipes, Redirects, and Here-Strings

### Redirects
- `command > out.txt` - overwrite output to file
- `command >> out.txt` - append output to file
- `command 2> err.txt` - stderr to file
- `command > out.txt 2>&1` - stdout+stderr to same file

### Pipes
- `command1 | command2` - send stdout of cmd1 into cmd2
- `cat file.txt | grep "x"` - example pipeline
- `ps aux | grep python` - filter processes (see also pgrep)

### Here-string / here-doc
- `grep "x" <<< "some text"` - feed literal text as stdin
- `cat <<'EOF' > file.txt` - write a block literally (no expansions)

## 5. Command Chaining & Exit Codes
- `command1; command2` - run both (always)
- `command1 && command2` - run cmd2 only if cmd1 succeeds
- `command1 || command2` - run cmd2 only if cmd1 fails
- `echo $?` - exit code of last command (0 = success)

## 6. Variables, Quotes, and Substitution (Bash basics)
- `name="George"` - set variable (NO spaces around =)
- `echo "$name"` - expand variable (quote to preserve spaces)
- `echo '${name}'` - single quotes prevent expansion
- `path="$HOME/projects"` - use env vars like $HOME
- `now="$(date)"` - capture command output
- `files_count="$(ls | wc -l)"` - combine commands
