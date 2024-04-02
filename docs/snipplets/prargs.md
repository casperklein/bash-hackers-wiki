# Print argument list for testing

---- dataentry snipplet ---- snipplet_tags: debug, arguments
LastUpdate_dt: 2013-03-23 Contributors: Snappy (IRC), Dan Douglas type:
snipplet

------------------------------------------------------------------------

Sometimes you might find it useful to see how arguments passed to a
program arrive there.

Check this script (save it as script file or make a function):

    printf '"%b"\n' "$0" "$@" | nl -v0 -s": "

It uses the [printf command](../commands/builtin/printf.md) to generate a
list of arguments, even with escape sequences interpreted. This list is
shown formatted by the nl(1) utility.

Another alternative with colorized output. If run in Bash, it
temporarily disables all debug output for itself, including the test
that determines whether to hide debug output. In ksh, tracing would have
to be enabled on the function to show debug output, so it works out to
being equivalent.

``` bash
# Bash or ksh93 debugging function for colored display of argv.
# Optionally set OFD to the desired output file descriptor.
function args {
    { BASH_XTRACEFD=3 command eval ${BASH_VERSION+"$(</dev/fd/0)"}; } <<-'EOF' 3>/dev/null
        case $- in *x*)
            set +x
            trap 'trap RETURN; set -x' RETURN
        esac
EOF
    
    [[ ${OFD-1} == +([0-9]) ]] || return

    if [[ -t ${OFD:-2} ]]; then
        typeset -A clr=([green]=$(tput setaf 2) [sgr0]=$(tput sgr0))
    else
        typeset clr
    fi

    if ! ${1+false}; then
        printf -- "${clr[green]}<${clr[sgr0]}%s${clr[green]}>${clr[sgr0]} " "$@"
        echo
    else
        echo 'no args.'
    fi >&"${OFD:-2}"
}
```
