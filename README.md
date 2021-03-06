# Lazy Loader

Speed up shell startup time by lazy loading parts of the environment.

Some CLI tools like version managers and environment-based prompt indicators
require initialization steps to be ran every time the environment is created.
These initialization steps take time and can significantly slow down startup
of each new shell instance.

With Lazy Loader, you can defer running the initialization steps for a given
command until the first time you attempt to run the command. For example, you
can delay loading rbenv until the first time you invoke `gem`, `rake`, or any
of the other commands that are managed by rbenv.

## Usage

To lazy load part of your environment, add a function to your `~/.bashrc` or
`~/.zshrc` containing the initialization step(s) for the tool you want to lazy
load. Then, pass that function to `lazy_load`, along with one or more commands
to use as triggers for running the initialization:

```bash
source "path/to/lazy_loader.sh"

rbenv_init() {
    eval "$(rbenv init -)"
}

lazy_load rbenv_init rbenv bundle bundler gem irb rake ruby
```

Lazy Loader works by creating a hook function for each trigger command:

```console
$ type ruby
ruby is a shell function from /Users/cyphus/.oh-my-zsh/custom/lazy-rbenv.zsh
```

When you invoke one of the trigger commands, the initialization function you
defined is ran, and the original command is called:

```console
$ ruby -v
Lazy loading ruby...
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-darwin16]
```

Any hook functions created by the lazy loader are removed so future invocations
will run the command directly:

```console
$ type ruby
ruby is /Users/cyphus/.rbenv/shims/ruby
```

## Motivation

It should come as no surprise that a tool to improve shell startup time was
born out of frustration with an existing setup. Between pyenv, rbenv, and nvm,
it was taking over a full second to spawn a new shell. I use all three tools
semi-regularly, but almost never use more than one of them in a single shell.

With Lazy Loader I was able to cut my startup time by over 90%, from 1.07
seconds to 102 milliseconds. Plus, Lazy Loader does this with one function and
no globals! I liked it enough that I thought it was worth sharing. Enjoy!
