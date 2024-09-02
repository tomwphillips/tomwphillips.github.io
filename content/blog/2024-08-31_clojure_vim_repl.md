+++
title = 'Learning Clojure: Vim REPL integration'
date = 2024-08-31T21:13:29+01:00
description = "Getting Clojure REPL integration with Vim and some namespace confusion."
[params]
	licence = "CC-BY-4.0"
+++

In my [last post]({{< ref "blog/2024-08-28_clojure_first_steps.md" >}}) I wrote that I was using the Cursive plugin for IntelliJ. It turned out that it doesn't play nicely with the Ideavim plugin. I considered mapping keystrokes to Cursive actions, but that's not an activity I enjoy and I doubted whether my keybindings would be any good.

Emacs is popular in the Clojure community, but I use Vim and I'm not willing to change editor to learn a new language. So I wanted a Vim plugin for REPL-driven development.

The hosts of the [Clojure Design Podcast](https://clojuredesign.club/episode/013-connect-the-repl/) use [Tim Pope's vim-fireplace](https://github.com/tpope/vim-fireplace), so I tried it out. You start a REPL with `lein repl`, which writes the port to a `.nrepl-port` file. vim-fireplace reads it and connects to the REPL. It worked as the README described but the keybindings didn't click for me.

After spotting the podcast was from 2019 and searching for something newer, I found [Conjure](https://github.com/Olical/conjure). In fact [one of the hosts blogged about switching to Conjure in 2023](https://endot.org/2023/05/27/vim-clojure-dev-2023/). Conjure is very interesting. It's written in [Fennel](https://fennel-lang.org/), a Lisp that compiles to [Lua](https://www.lua.org/) (so it's for Neovim, not Vim). Conjure supports REPL-driven development for a bunch of different Lisps and somehow even [Python](https://github.com/Olical/conjure/wiki/Quick-start:-Python-(stdio)), which I need to try.

Here I wasted loads of time reluctantly fiddling with Vim configs. I should definitely have [tried Conjure out without installing](https://neovim.io/doc/user/nvim.html#nvim-from-vim). You should try this first!

Long story short: I didn't want to move my Python development from PyCharm to Vim, so I settled on three vim config files: separate Ideavim and Neovim configs that both source a common config file of common preferences and keybindings. [Ideavim supports a small number of plugins via `vim-plug`](https://github.com/JetBrains/ideavim/wiki/IdeaVim-Plugins), so I figured it was easiest to use it in Neovim too.

Luckily, after all the fiddling, the Conjure keybindings made sense to me. Type `<localleader>e` to "evaluate" then pick the thing to evaluate with another key:

* `,eb` – evaluate entire file
* `,ee` – evaluate inner form under cursor
* `,er` – evaluate outer most (root) form under cursor
* `,ew` – evaluate word, e.g. to peek in a var

I like it!

## Namespaces

Here's something I found confusing.

I've got a project I started with Leinigen. I start up the REPL with `lein repl` and type `(doc frequencies)`. It prints out the documentation for `frequencies`. Evaluating `*ns*` tells me I'm in the `noughts-and-crosses.core` namespace, but somehow Clojure still resolves `doc` from the `clojure.repl` namespace.

Now I open up `src/noughts_and_crosses/model.clj` in vim. At the start of the file the namespace is defined with `(ns noughts-and-crosses.model)`. I type `(doc frequencies)` into the file then evaluate it  (`,ee`) and Clojure complains it can't resolve `doc`! Evaluating `*ns*` tells me I'm in the `noughts-and-crosses.model` namespace, so something is switching me into the file's namespace, but _isn't_ loading `doc` from `clojure.repl`.

I couldn't get to the bottom of *why* this happens. I suppose the Leinigen REPL loads a bunch of namespaces for convenience.
