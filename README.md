# elmenv

Simple management of Elm versions, inspired by projects like
[rbenv](https://github.com/sstephenson/rbenv),
[nvm](https://github.com/creationix/nvm), and
[phpenv](https://github.com/phpenv/phpenv).

## How It Works

At a high level, elmenv intercepts Elm commands using shim
executables injected into your `PATH`, determines which Elm version
has been specified by your application, and passes your commands along
to the correct Elm installation.

### Understanding Shims

elmenv works by inserting a directory of _shims_ at the front of your
`PATH`:

    ~/.elmenv/shims:/usr/local/bin:/usr/bin:/bin

Through a process called _rehashing_, elmenv maintains shims in that
directory to match every Elm command across every installed version
of Elm—`elm-make`, `elm-reactor`, `elm-package`, `elm-reply`, `elm`,
and so on.

Shims are lightweight executables that simply pass your command along
to elmenv. So with elmenv installed, when you run, say, `elm`, your
operating system will do the following:

* Search your `PATH` for an executable file named `elm`
* Find the elmenv shim named `elm` at the beginning of your `PATH`
* Run the shim named `elm`, which in turn passes the command along to
  elmenv

### Choosing the Elm Version

When you execute a shim, elmenv determines which Elm version to use by
reading it from the following sources, in this order:

1. The `ELMENV_VERSION` environment variable, if specified. You can use
   the [`elmenv shell`](#elmenv-shell) command to set this environment
   variable in your current shell session.

2. The first `.elm-version` file found by searching the directory of the
   script you are executing and each of its parent directories until reaching
   the root of your filesystem.

3. The first `.elm-version` file found by searching the current working
   directory and each of its parent directories until reaching the root of your
   filesystem. You can modify the `.elm-version` file in the current working
   directory with the [`elmenv local`](#elmenv-local) command.

4. The global `~/.elmenv/version` file. You can modify this file using
   the [`elmenv global`](#elmenv-global) command. If the global version
   file is not present, elmenv assumes you want to use the "system"
   Elm—i.e. whatever version would be run if elmenv weren't in your
   path.

## Installation

This will get you going with the latest version of elmenv and make it
easy to fork and contribute any changes back upstream.

1. Check out elmenv into `~/.elmenv`.

    ~~~ sh
    $ git clone --recurse-submodules https://github.com/sonnym/elmenv.git ~/.elmenv
    ~~~

2. Add `~/.elmenv/bin` to your `$PATH` for access to the `elmenv`
   command-line utility.

    ~~~ sh
    $ echo 'export PATH="$HOME/.elmenv/bin:$PATH"' >> ~/.bash_profile
    ~~~

    **Ubuntu Desktop note**: Modify your `~/.bashrc` instead of `~/.bash_profile`.

    **Zsh note**: Modify your `~/.zshrc` file instead of `~/.bash_profile`.

3. Add `elmenv init` to your shell to enable shims and autocompletion.

    ~~~ sh
    $ echo 'eval "$(elmenv init -)"' >> ~/.bash_profile
    ~~~

    _Same as in previous step, use `~/.bashrc` on Ubuntu, or `~/.zshrc` for Zsh._

4. Restart your shell so that PATH changes take effect. (Opening a new
   terminal tab will usually do it.) Now check if elmenv was set up:

    ~~~ sh
    $ type elmenv
    #=> "elmenv is a function"
    ~~~

#### Upgrading

If you've installed elmenv manually using git, you can upgrade your
installation to the cutting-edge version at any time.

~~~ sh
$ cd ~/.elmenv
$ git pull
$ git submodule foreach $(git submodule update --init --recursive)
~~~

## Installing Elm

To install a Elm version for use with elmenv, run `elmenv install` with the
exact name of the version you want to install. For example,

    elmenv install 0.14.1

Elm versions will be installed into a directory of the same name under
`~/.elmenv/versions`. It is also possible install the `master` branch of
elm. The installation is directly from source, so it is necessary to have
an up to date haskell and cabal setup.
