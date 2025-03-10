# Name

`nix-env` - manipulate or query Nix user environments

# Synopsis

`nix-env` *operation* [*options*] [*arguments…*]
  [`--option` *name* *value*]
  [`--arg` *name* *value*]
  [`--argstr` *name* *value*]
  [{`--file` | `-f`} *path*]
  [{`--profile` | `-p`} *path*]
  [`--system-filter` *system*]
  [`--dry-run`]

# Description

The command `nix-env` is used to manipulate Nix user environments. User
environments are sets of software packages available to a user at some
point in time. In other words, they are a synthesised view of the
programs available in the Nix store. There may be many user
environments: different users can have different environments, and
individual users can switch between different environments.

`nix-env` takes exactly one *operation* flag which indicates the
subcommand to be performed. The following operations are available:

- [`--install`](./nix-env/install.md)
- [`--upgrade`](./nix-env/upgrade.md)
- [`--uninstall`](./nix-env/uninstall.md)
- [`--set`](./nix-env/set.md)
- [`--set-flag`](./nix-env/set-flag.md)
- [`--query`](./nix-env/query.md)
- [`--switch-profile`](./nix-env/switch-profile.md)
- [`--list-generations`](./nix-env/list-generations.md)
- [`--delete-generations`](./nix-env/delete-generations.md)
- [`--switch-generation`](./nix-env/switch-generation.md)
- [`--rollback`](./nix-env/rollback.md)

These pages can be viewed offline:

- `man nix-env-<operation>`.

  Example: `man nix-env-install`

- `nix-env --help --<operation>`

  Example: `nix-env --help --install`

# Selectors

Several commands, such as `nix-env -q` and `nix-env -i`, take a list of
arguments that specify the packages on which to operate. These are
extended regular expressions that must match the entire name of the
package. (For details on regular expressions, see **regex**(7).) The match is
case-sensitive. The regular expression can optionally be followed by a
dash and a version number; if omitted, any version of the package will
match. Here are some examples:

  - `firefox`\
    Matches the package name `firefox` and any version.

  - `firefox-32.0`\
    Matches the package name `firefox` and version `32.0`.

  - `gtk\\+`\
    Matches the package name `gtk+`. The `+` character must be escaped
    using a backslash to prevent it from being interpreted as a
    quantifier, and the backslash must be escaped in turn with another
    backslash to ensure that the shell passes it on.

  - `.\*`\
    Matches any package name. This is the default for most commands.

  - `'.*zip.*'`\
    Matches any package name containing the string `zip`. Note the dots:
    `'*zip*'` does not work, because in a regular expression, the
    character `*` is interpreted as a quantifier.

  - `'.*(firefox|chromium).*'`\
    Matches any package name containing the strings `firefox` or
    `chromium`.

# Files

  - `~/.nix-defexpr`\
    The source for the default Nix expressions used by the
    `--install`, `--upgrade`, and `--query --available` operations to
    obtain derivations. The `--file` option may be used to override
    this default.

    If `~/.nix-defexpr` is a file, it is loaded as a Nix expression. If
    the expression is a set, it is used as the default Nix expression.
    If the expression is a function, an empty set is passed as argument
    and the return value is used as the default Nix expression.

    If `~/.nix-defexpr` is a directory containing a `default.nix` file,
    that file is loaded as in the above paragraph.

    If `~/.nix-defexpr` is a directory without a `default.nix` file,
    then its contents (both files and subdirectories) are loaded as Nix
    expressions. The expressions are combined into a single set, each
    expression under an attribute with the same name as the original
    file or subdirectory.

    For example, if `~/.nix-defexpr` contains two files, `foo.nix` and
    `bar.nix`, then the default Nix expression will essentially be

    ```nix
    {
      foo = import ~/.nix-defexpr/foo.nix;
      bar = import ~/.nix-defexpr/bar.nix;
    }
    ```

    The file `manifest.nix` is always ignored. Subdirectories without a
    `default.nix` file are traversed recursively in search of more Nix
    expressions, but the names of these intermediate directories are not
    added to the attribute paths of the default Nix expression.

    The command `nix-channel` places symlinks to the downloaded Nix
    expressions from each subscribed channel in this directory.

  - `~/.nix-profile`\
    A symbolic link to the user's current profile. By default, this
    symlink points to `prefix/var/nix/profiles/default`. The `PATH`
    environment variable should include `~/.nix-profile/bin` for the
    user environment to be visible to the user.
