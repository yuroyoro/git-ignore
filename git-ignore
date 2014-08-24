#!/bin/bash

# Copyright (c) 2014 Tomohito Ozaki(yuroyoro).
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
#  1. Redistributions of source code must retain the above copyright
#     notice, this list of conditions and the following disclaimer.
#  2. Redistributions in binary form must reproduce the above copyright
#     notice, this list of conditions and the following disclaimer in
#     the documentation and/or other materials provided with the
#     distribution.
#  3. The name of the author may not be used to endorse or promote
#     products derived from this software without specific prior
#     written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE AUTHOR ``AS IS'' AND ANY EXPRESS
# OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY
# DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
# GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER
# IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
# OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN
# IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

#
# (Command) Show usage
#
usage() {
  cat << EOS
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
EOS
}

#
# (Command) Print ignore patterns
#
list() {
    cat ~/.gitignore   | grep -v '^\s*$' | sed -e "s/^/\(global\) /g"
    if [ -f "${gitignore}" ] ; then
      cat "${gitignore}" | grep -v '^\s*$' | sed -e "s/^/\(local\)  /g"
    fi
}

#
# (Command) Add ignore patterns to .gitignore
#
add() {
    target="${gitignore}"
    if [ ${global} -eq 1 ] ; then
        target="${HOME}/.gitignore"
    else
        if [ -z "${work_dir}" ] ; then
            echo "(error) Not a git repository (or any of the parent directories)"
            exit 1
        fi
    fi

    if [ ! -f "${target}" ] ; then
        touch "${target}"
        echo "Created ${target}"
    fi

    i=0
    for pat in ${patterns[@]}; do
        add_pattern ${target} "${pat}"
    done
}

#
# (Command) Remove ignore patterns from .gitignore
#
remove() {
    target="${gitignore}"
    if [ ${global} -eq 1 ] ; then
        target="${HOME}/.gitignore"
    else
        if [ -z "${work_dir}" ] ; then
            echo "(error) Not a git repository (or any of the parent directories)"
            exit 1
        fi
    fi

    if [ ! -f "${target}" ] ; then
        echo "(error) ${target} is not found"
        exit 1
    fi

    i=0
    for pat in ${patterns[@]}; do
        remove_pattern "${target}" "${pat}"
    done
}

#
# (Command) Remove ignore patterns from .gitignore
#
pull() {
    lang="${patterns[0]}"

    # show availale lists
    if [ -z "${lang}" ] ; then
        echo "Available list : "
        echo ""
        curl -sL https://api.github.com/repos/github/gitignore/contents | grep "\"name\":.\+\.gitignore" | sed -e 's/^[ ]*\"name\"\:[ ]*\"\(.*\)\.gitignore\",/\1/g' | tr '\n' '\t'

        echo ""
        echo ""
        echo "Available list(global) : "
        echo ""
        curl -sL https://api.github.com/repos/github/gitignore/contents/Global | grep "\"name\":.\+\.gitignore" | sed -e 's/^[ ]*\"name\"\:[ ]*\"\(.*\)\.gitignore\",/\1/g' | sed -e "s/^/Global\//g" |  tr '\n' '\t'
        exit 0
    fi

    target="${gitignore}"
    if [ ${global} -eq 1 ] ; then
        target="${HOME}/.gitignore"
    else
        if [ -z "${work_dir}" ] ; then
            echo "(error) Not a git repository (or any of the parent directories)"
            exit 1
        fi
    fi

    if [ ! -f "${target}" ] ; then
        touch "${target}"
        echo "Created ${target}"
    fi

    IFS=$'\n'
    url="https://raw.githubusercontent.com/github/gitignore/master/${lang}.gitignore"
    res=(`curl -sfL "${url}"`)

    if [ $? -ne 0 ]; then
        echo "(error) ${url} is Not Found"
        exit 1
    fi

    echo "# Added from ${url}" >> ${target}
    for pat in ${res[@]}; do
        case "${pat}" in
            \#*)  ;; # comment, do nothing
            *)    add_pattern ${target} "${pat}" ;;
        esac
    done
}

#
# (private) Setup working dir and local .gitignore path
#
set_ignorefile() {
    work_dir="`git rev-parse --show-toplevel 2> /dev/null`"
    if [ $? -eq 0 ]; then
        gitignore="${work_dir}/.gitignore"
    fi
}

#
# (private) Add a pattern to target .gitignore
#
add_pattern() {
    target=$1
    pat=$2

    res="`grep \"^${pat}$\" ${target} > /dev/null`"
    if [ $? -ne 0 ]; then
        echo "${pat}" >> ${target}
        echo "Added a pattern (${pat}) to ${target}"
    else
        echo "(warning) Given pattern (${pat}) is already exists in ${target}"
    fi
}

#
# (private) Revmoe a pattern from target .gitignore
#
remove_pattern() {
    target=$1
    pat=$2

    res="`grep \"^${pat}$\" ${target} > /dev/null`"
    if [ $? -ne 0 ]; then
        echo "(warning) Given pattern (${pat}) is not exists in ${target}"
    else
        res="`sed -i -e s/^"${pat}"$//g ${target}`"
        if [ $? -eq 0 ]; then
            echo "Remove a pattern (${pat}) from ${target}"
        fi
    fi
}

patterns=()
global=0

# parse options/command/patterns
for o in $*
do
  case "${o}" in
    -g|--global)               global=1   ;;
    help|add|remove|list|pull) cmd="${o}" ;;
    * )                        patterns+=("${o}")
  esac
done

if [ -z "${cmd}" ] ; then
    usage
else
  case "$cmd" in
    help)     usage  ;;
    add)      set_ignorefile ; add    ;;
    remove)   set_ignorefile ; remove ;;
    list)     set_ignorefile ; list   ;;
    pull)     set_ignorefile ; pull   ;;
    * )       usage  ;;
  esac
fi
