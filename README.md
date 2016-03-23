git-ignore
==========

git extension command for manage `.gitignore` entries.

## Synopsis

`git-ignore` is git extension command to Add/Remove ignorance pattenrs to your `.gitignore.`

![](https://raw.githubusercontent.com/yuroyoro/git-ignore/master/demo.gif)

## Usage

```
$ git ignore
Usage :
  git ignore [options] command [patterns..]

COMMANDS:
   list         Print ignore patterns
   add          Add pattens to .gitignore
   remove       Remove patterns from .gitignore
   pull         Pull gitignore from https://github.com/github/gitignore
   help         Shows a list of commands or help

GLOBAL OPTIONS:
   --global, -g    Add/Remove patterns to global .gitignore($HOME/.gitignore)
```

## Installation

To install, put this script to your $PATH.

```
curl -sL https://raw.githubusercontent.com/yuroyoro/git-ignore/master/git-ignore > ~/bin/git-ignore
chmod a+x ~/bin/git-ignore
```

## Example

You can add a pattern to just run `git ignore add "pattern"`.

```
$ git ignore add '*.log'
```

To Remove entry, just run `git ignore remove "pattern"`.

```
$ git ignore remove '*.log'
```

You can add/remove multiple patterns just once run.

```
$ git ignore add '*.log' '*.bak'
```

`git ignore list` shows entries in your `.gitignore`


```
$ git ignore list
```

`--global` option enables to add/remove entiries to global `.gitignore` file (`$HOME/.gitignore`).
Following command adds a pattern `*.class` to your `$HOME/.gitignore`.

```
git ignore --global add "*.class"
```

`git ignore pull <lang>` command is fetch recommended `.gitignore` file from [github/gitignore](https://github.com/github/gitignore).
Follwing example fetch patterns from https://github.com/github/gitignore/Haskell.gitignore and put it to `.gitignore`

```
$ git ignore pull Haskell
```

When no `lang` argument pass to `git ignore pull` command, it shows available lanuage list.

```
$ git ignore pull
```

## Author

[yuroyoro](https://github.com/yuroyoro)

Pull Requests are always welcome!!
