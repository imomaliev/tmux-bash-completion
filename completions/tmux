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
    local cur="${1}"
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(tmux list-clients -F '#{client_tty}' 2>/dev/null)" -- "${cur}") )
}

function _tmux_complete_session() {
    local IFS=$'\n'
    local cur="${1}"
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(tmux list-sessions -F '#{session_name}' 2>/dev/null)" -- "${cur}") )
}

function _tmux_complete_window() {
    local IFS=$'\n'
    local cur="${1}"
    local session_name="$(echo "${cur}" | sed 's/\\//g' | cut -d ':' -f 1)"
    local sessions

    sessions="$(tmux list-sessions 2>/dev/null | sed -re 's/([^:]+:).*$/\1/')"
    if [[ -n "${session_name}" ]]; then
        sessions="${sessions}
        $(tmux list-windows -t "${session_name}" 2>/dev/null | sed -re 's/^([^:]+):.*$/'"${session_name}"':\1/')"
    fi
    cur="$(echo "${cur}" | sed -e 's/:/\\\\:/')"
    sessions="$(echo "${sessions}" | sed -e 's/:/\\\\:/')"
    COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "${sessions}" -- "${cur}") )
}

_tmux() {
    local cur prev
    local i cmd cmd_index option option_index
    local opts=""
    COMPREPLY=()
    cur="${COMP_WORDS[COMP_CWORD]}"
    prev="${COMP_WORDS[COMP_CWORD-1]}"

    if [ ${prev} != -f ]; then
    # Search for the command
    local skip_next=0
    for ((i=1; $i<=$COMP_CWORD; i++)); do
        if [[ ${skip_next} -eq 1 ]]; then
            #echo "Skipping"
            skip_next=0;
        elif [[ ${COMP_WORDS[i]} != -* ]]; then
            cmd="${COMP_WORDS[i]}"
            cmd_index=${i}
            break
        elif [[ ${COMP_WORDS[i]} == -f ]]; then
            skip_next=1
        fi
    done

    # Search for the last option command
    skip_next=0
    for ((i=1; $i<=$COMP_CWORD; i++)); do
        if [[ ${skip_next} -eq 1 ]]; then
            #echo "Skipping"
            skip_next=0;
        elif [[ ${COMP_WORDS[i]} == -* ]]; then
            option="${COMP_WORDS[i]}"
            option_index=${i}
            if [[ ${COMP_WORDS[i]} == -- ]]; then
                break;
            fi
        elif [[ ${COMP_WORDS[i]} == -f ]]; then
            skip_next=1
        fi
    done

    if [[ $COMP_CWORD -le $cmd_index ]]; then
        # The user has not specified a command yet
        COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "$(tmux start \; list-commands | cut -d' ' -f1)" -- "${cur}") )
    else
        case ${cmd} in
            attach-session|attach)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" ;;
                *) options="-t -d" ;;
            esac ;;
            detach-client|detach)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            lock-client|lockc)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            lock-session|locks)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" ;;
                *) options="-t -d" ;;
            esac ;;
            new-session|new)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" ;;
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
                -t) _tmux_complete_client "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            rename-session|rename)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            has-session|has|kill-session)
            case "$prev" in
                -t) _tmux_complete_session "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            suspend-client|suspendc)
            case "$prev" in
                -t) _tmux_complete_client "${cur}" ;;
                *) options="-t" ;;
            esac ;;
            switch-client|switchc)
            case "$prev" in
                -c) _tmux_complete_client "${cur}" ;;
                -t) _tmux_complete_session "${cur}" ;;
                *) options="-l -n -p -c -t" ;;
            esac ;;

            send-keys|send)
            case "$option" in
                -t) _tmux_complete_window "${cur}" ;;
                *) options="-t" ;;
            esac ;;
          esac # case ${cmd}
        fi # command specified
      fi # not -f

      if [[ -n "${options}" ]]; then
          COMPREPLY=( ${COMPREPLY[@]:-} $(compgen -W "${options}" -- "${cur}") )
      fi

      return 0

}
# http://linux.die.net/man/1/bash
complete -o default -F _tmux tmux

# END tmux completion
