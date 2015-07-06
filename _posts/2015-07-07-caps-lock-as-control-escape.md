---
layout: post
title: "Caps Lock as both Control and Escape"
categories: tutorial
tags: vim tmux terminal
---

Two tools I can't be without for getting some coding done is [Vim][vim] as my
text editor and [tmux][tmuxl] for managing my [terminal][iterm2].

For those who don't know what tmux is, they summarize it pretty well:

> It [tmux] lets you switch easily between several programs in one terminal,
> detach them (they keep running in the background) and reattach them to a
> different terminal. And do a lot more.

Vim uses the Escape key an awful lot (to exit the different modes). tmux
prefixes every command with `ctrl-b` as default, even though it's a little
awkward to hit.

These two keys (Escape and Control) get a lot of attention, but are annoying to
reach. What key instead is placed on the home row, easy to reach and rarely (if
ever) being used? That's right: **Caps Lock**. So I remapped Caps Lock to work
as both Escape and Control and I have never looked back. I've never encountered
a problem that required me to turn it off, even temporarily.


## Remapping Caps Lock on OS X

It's rather easy to setup on OS X.

First go into **System Preferences... > Keyboard > Modifier Keys...** and change
**Caps Lock** to **Control** as shown.

![Caps Lock modifier settings](/images/caps-lock-modifier.png)

After that, install [Karabiner][karabiner] (also available as a [Homebrew
cask][cask] `brew cask install karabiner`) and open its preferences. Search for
**control escape** and enable the **Control\_L to Control\_L** option.

![Karabiner preferences](/images/karabiner-control-escape.png)

That's it. Give it a try; with Vim, tmux, your ordinary apps, see how it works
for you.


## Tip for tmux
Consider changing the prefix to `ctrl-a` instead. It works really well with the
Caps Lock remap and it's easy to hit. See my [dotfiles][dotfiles] for how to
change that.

[vim]:       http://www.vim.org/
[tmuxl]:     https://tmux.github.io/
[iterm2]:    https://www.iterm2.com/
[Karabiner]: https://pqrs.org/osx/karabiner/
[cask]:      http://caskroom.io/
[dotfiles]:  https://github.com/lauritzsh/dotfiles/blob/df1e0e1838f8ed6c2ec20bc3d2107d20a67565f0/tmux/tmux.conf#L30-L32
