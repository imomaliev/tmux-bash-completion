#!/usr/bin/env bash

# tmux completion
# See: http://www.debian-administration.org/articles/317 for how to write more.
# Usage: Put "source $PATH_TO_TMUX_BASH_COMPLETION/completions/tmux" into your .bashrc

# based on https://github.com/przepompownia/tmux-bash-completion/blob/master/completions/tmux
# with fix https://gist.github.com/eparis/fd17b8fb4eb58efc2c12

__tmux_init_completion()
{
    COMPREPLY=()
    _get_comp_words_by_ref cur prev words cword
}

_tmux()
{
	local cur prev words cword;
    if declare -F _init_completions >/dev/null 2>&1; then
        _init_completion
    else
        __tmux_init_completion
    fi
	if [[ $cword -eq 1 ]]; then
		COMPREPLY=($( compgen -W "$(tmux start\; list-commands | cut -d' ' -f1)" -- "$cur" ));
		return 0
	fi
}
complete -F _tmux tmux
