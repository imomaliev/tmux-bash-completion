#!/usr/bin/env bash

# Copy of https://github.com/Bash-it/bash-it/blob/master/completion/available/tmux.completion.bash
# and https://github.com/przepompownia/bash-it/blob/master/completion/available/tmux.completion.bash
# slightly refactored

# tmux completion
# See: http://www.debian-administration.org/articles/317 for how to write more.
# Usage: Put "source bash_completion_tmux.sh" into your .bashrc
# Based upon the example at http://paste-it.appspot.com/Pj4mLycDE

function _tmux_complete_client() {
    local IFS=$'\n'
    local cur="${1}" && shift
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(tmux "$@" list-clients -F '#{client_tty}' 2> /dev/null)" -- "${cur}") )
    options=""
    return 0
}

function _tmux_complete_session() {
    local IFS=$'\n'
    local cur="${1}" && shift
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(tmux "$@" list-sessions -F '#{session_name}' 2> /dev/null)" -- "${cur}") )
    options=""
    return 0
}

function _tmux_complete_window() {
    local IFS=$'\n'
    local cur="${1}" && shift
    local session_name="$(echo "${cur}" | sed 's/\\//g' | cut -d ':' -f 1)"
    local sessions

    sessions="$(tmux "$@" list-sessions 2> /dev/null | sed -re 's/([^:]+:).*$/\1/')"
    if [[ -n "${session_name}" ]]; then
        sessions="${sessions}
        $(tmux "$@" list-windows -t "${session_name}" 2> /dev/null | sed -re 's/^([^:]+):.*$/'"${session_name}"':\1/')"
    fi
    cur="$(echo "${cur}" | sed -e 's/:/\\\\:/')"
    sessions="$(echo "${sessions}" | sed -e 's/:/\\\\:/')"
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "${sessions}" -- "${cur}") )
    options=""
    return 0
}

function _tmux_complete_socket_name() {
    local IFS=$'\n'
    local cur="${1}" && shift
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(find /tmp/tmux-$UID -type s -printf '%P\n')" -- "${cur}") )
    options=""
    return 0
}
function _tmux_complete_socket_path() {
    local IFS=$'\n'
    local cur="${1}" && shift
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(find /tmp/tmux-$UID -type s -printf '%p\n')" -- "${cur}") )
    options=""
    return 0
}

__tmux_init_completion()
{
    COMPREPLY=()
    _get_comp_words_by_ref cur prev words cword
}

_tmux() {
    local cur prev words cword;
    if declare -F _init_completion >/dev/null 2>&1; then
        _init_completion
    else
        __tmux_init_completion
    fi

    local index=1
    # Check tmux options that will change completion for:
    # - available sessions
    # - available windows
    # - ...
    local argv=( "${words[@]:1}" )
    local OPTIND OPTARG OPTERR=0 flag tmux_args=()
    while getopts "L:S:" flag "${argv[@]}"; do
        case "$flag" in
            L) tmux_args+=(-L "$OPTARG") ;;
            S) tmux_args+=(-S "$OPTARG") ;;
            *) ;;
        esac
    done
    # Completed -- have a space after
    if [[ ${#words[@]} -gt $OPTIND ]]; then
        local tmux_argc=${#tmux_args[@]}
        (( index+=tmux_argc ))
        (( cword-=tmux_argc ))
    fi

    if [[ $cword -eq 1 ]]; then
        COMPREPLY=($( compgen -W "$(tmux start\; list-commands | cut -d' ' -f1)" -- "$cur" ));
        return 0
    else
        case ${words[index]} in
            -L) _tmux_complete_socket_name "${cur}" ;;
            -S) _tmux_complete_socket_path "${cur}" ;;

            attach-session|attach)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                *) options="-t -d" ;;
            esac ;;
            detach-client|detach)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            lock-client|lockc)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            lock-session|locks)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                *) options="-t -d" ;;
            esac ;;
            new-session|new)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                -[n|d|s]) options="-d -n -s -t --" ;;
                *)
                if [[ ${COMP_WORDS[option_index]} == -- ]]; then
                    _command_offset ${option_index}
                else
                    options="-d -n -s -t --"
                fi
                ;;
            esac
            ;;
            refresh-client|refresh)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            rename-session|rename)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            has-session|has|kill-session)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            source-file|source)
                _filedir ;;
            suspend-client|suspendc)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
            switch-client|switchc)
            case "$prev" in
                -c) _tmux_complete_client "${cur}" "${tmux_args[@]}" ;;
                -t) _tmux_complete_session "${cur}" "${tmux_args[@]}" ;;
                *) options="-l -n -p -c -t" ;;
            esac ;;

            send-keys|send)
            case "$option" in
                -t) _tmux_complete_window "${cur}" "${tmux_args[@]}" ;;
                *) options="-t" ;;
            esac ;;
        esac # case ${cmd}
    fi # command specified

    if [[ -n "${options}" ]]; then
        COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "${options}" -- "${cur}") )
    fi

    return 0
}
# http://linux.die.net/man/1/bash
complete -F _tmux tmux

# END tmux completion
