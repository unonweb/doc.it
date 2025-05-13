# files

```sh
ls ~/.bash_profile # doesn't exist
ls ~/.bash_login # doesn't exist

cat ~/.profile
```

# completion

```sh
sudo apt install bash-completion
```

bash completion for sudo:

```sh
# .bashrc
complete -cf sudo
```
# aliases

The problem: Aliases in Zsh and Bash are normally only expanded for the first word in a command. This means that your aliases will not normally get expanded when running a command with `sudo`. 

Solution: One way to make the next word expand is to make an alias for sudo ending with a space. Add the following to your shell's configuration file:

```sh
alias sudo='sudo ' # enable aliases for commands prefixed with sudo
```

zshmisc(1) § ALIASING describes how this works:

> If the replacement text ends with a space, the next word in the shell input is always eligible for purposes of alias expansions.

As well as bash(1) § ALIASES:
> If the last character of the alias value is a blank, then the next command word following the alias is also checked for alias expansion.

# shopt

**set/unset shell options**; 

```sh
shopt # lists all shell options with an indication if set or not

shopt -s <option> # sets *option*
shopt -u <option> # unsets option
```

## options

**autocd** # If set, a command name that is the name of a directory is executed as if it were the argument to the cd command. This option is only used by interactive shells.

**cdspell** # If set, minor errors in the spelling of a directory component in a cd command will be corrected. The errors checked for are transposed characters, a missing character, and a character too many. If a correction is found, the corrected path is printed, and the command proceeds. This option is only used by interactive shells.

**dirspell** # If set, Bash attempts spelling correction on directory names during word completion if the directory name initially supplied does not exist.

**expand_aliases** # If set, aliases are expanded as described below under Aliases, Aliases. This option is enabled by default for interactive shells.

**extquote** # If set, $'string' and $"string" quoting is performed within ${parameter} expansions enclosed in double quotes. This option is enabled by default.

**huponexit** # If set, Bash will send SIGHUP to all jobs when an interactive login shell exits

**login_shell** # The shell sets this option if it is started as a login shell (see Invoking Bash). The value may not be changed.

**progcomp** # If set, the programmable completion facilities are enabled. This option is enabled by default.

**progcomp_alias** # If set, and programmable completion is enabled, Bash treats a command name that doesn’t have any completions as a possible alias and attempts alias expansion. If it has an alias, Bash attempts programmable completion using the command word resulting from the expanded alias.

**xpg_echo** # If set, the echo builtin expands backslash-escape sequences by default.

### pattern matching/globs

**failglob** # If set, patterns which fail to match filenames during filename expansion result in an expansion error.

**globstar** # If set, the pattern ** used in a filename expansion context will match all files and zero or more directories and subdirectories. If the pattern is followed by a ‘/’, only directories and subdirectories match.

**extglob** # If set, the extended pattern matching features described above are enabled.

**dotglob** # If set, Bash includes filenames beginning with a ‘.’ in the results of filename expansion. The filenames ‘.’ and ‘..’ must always be matched explicitly, even if dotglob is set.

### history

**histappend** # If set, the history list is appended to the file named by the value of the HISTFILE variable when the shell exits, rather than overwriting the file.

**histreedit** # If set, and Readline is being used, a user is given the opportunity to re-edit a failed history substitution.

**histverify** # If set, and Readline is being used, the results of history substitution are not immediately passed to the shell parser. Instead, the resulting line is loaded into the Readline editing buffer, allowing further modification.

**lithist** # If enabled, and the cmdhist option is enabled, multi-line commands are saved to the history with embedded newlines rather than using semicolon separators where possible.
