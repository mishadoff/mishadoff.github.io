---
layout: post
title: "Mnemonics-based keymap for IDEA"
date: 2018-03-12 12:20
comments: true
categories: [programming, idea, clojure, cursive, productivity]
sharing: true
published: false
---

Emacs is great for editing text, IDEA is great for navigation.  
Configured IDEA could behave like Emacs (and even better!)

<!-- more -->

# Rationale

> If you can configure it, you will be configuring it  
>                          - Anonymous, about Linux  

Emacs is keyboard-driven editor, meaning all actions you can do without mouse or touchpad. It is a good part.
The bad part is it lacks rich navigation capabilities, integration with external tools, code aware completion, etc.
Well, you still can try hundred of plugins and configure exactly what you need, or try tens of prebuild emacs-based
distributions with batteries included, but in IntelliJ it just works out of the box.

IDEA has most things that missing in Emacs and still considered as the best IDE for Java. 
Even more, recently I tried Cursive extension for Clojure and it works really well.

The most annoying things in IDEA is an editor and interaction with tool windows, I mean _you need mouse_.
You still can try emacs-based keymap, but you will be using mouse/touchpad extensively. 
Your fingers will be screaming. Especially, if you work on the latest MacBook Pro.

I've tried to develop mnemonics-based keymap (a.k.a Emacs style) for IntelliJ, 
to reduce mouse interaction to minimum, while providing a simple way to remember the hotkeys.

# Preparation

I'm a _happy_ user of MacBook Pro 13'' (2017). 
Really, I find MacOS still the best system for people and devs, but this new keyboard drive me crazy.

Ironically, Escape button escaped to virtual world, so you can't get tactile feedback from it.
 But you have discount section, where you got four arrows in the ~~price~~ place of three.
Greedy backspace, which steal all your home row attention if you want just delete a char. 
Small vertical enter.

I can continue ranting about non-consistent sound from different keypresses, 
but thanks to [this blog](http://tonsky.me/blog/cursor-keys/)
I remapped most of this keys, and happy so far.

Shortly, CapsLock remapped to Control (do you know people who use CapsLock?), 
then Control-IJKL become normal arrows, Control-H as a backspace (hello Emacs users), 
Control-M and Control-N work in pair as a horizontal Enter, and the same way Control-[ and Control-] work like Escape.
Also, I don't use right Command, so it also became Control, now you can use arrows with one hand while... eating or so.     

This may sound strange configuration, but in my opinion it works like a charm.  
Below, I list all hotkeys. Hover on picture to activate a gif with demo.

<style>
.bordered { border: 1px grey solid; }
</style>

# Basic hotkeys (C- prefix)

Basic hotkeys triggered with Control prefix. (`C-a` means press "A" while holding Control key)  
This category exposes basic keybindings for navigating and editing text in editor.

## `C-a` - move cursor to the start of line

"A" is always beginning of something, line in this case.

<style> 
.static-c-a { position:absolute; background: white; }
.static-c-a:hover { opacity:0; } 
</style>
<img class="bordered static-c-a" src="https://i.imgur.com/7t9IO9p.gif">
<img class="bordered active-c-a" src="https://i.imgur.com/42M6Fp1.gif"> 

## `C-b` - move cursor one word backward

"B" for backward. For long sentences it's faster than arrows.  

<style> 
.static-c-b { position:absolute; background: white; }
.static-c-b:hover { opacity:0; } 
</style>
<img class="bordered static-c-b" src="https://i.imgur.com/xCRbuwj.gif">
<img class="bordered active-c-b" src="https://i.imgur.com/ZyW49TT.gif">   

## `C-c` - prefix, reserved for Code mode
  
## `C-d` - delete character (forward)

"D" for delete. Because, my MacBook keyboard doesn't have delete button, only backspace.

<style> 
.static-c-d { position:absolute; background: white; }
.static-c-d:hover { opacity:0; } 
</style>
<img class="bordered static-c-d" src="https://i.imgur.com/aomeGmJ.gif">
<img class="bordered active-c-d" src="https://i.imgur.com/e5JLMNA.gif">
    
## `C-e` - move cursor to the end of line

"E" for end. Opposite to "A".

<style> 
.static-c-e { position:absolute; background: white; }
.static-c-e:hover { opacity:0; } 
</style>
<img class="bordered static-c-e" src="https://i.imgur.com/D2gmheS.gif">
<img class="bordered active-c-e" src="https://i.imgur.com/zVt6P0D.gif">
  
## `C-f` - move cursor one word forward  

"F" for forward. Opposite to "B".

<style> 
.static-c-f { position:absolute; background: white; }
.static-c-f:hover { opacity:0; } 
</style>
<img class="bordered static-c-f" src="https://i.imgur.com/r95hbSO.gif">
<img class="bordered active-c-f" src="https://i.imgur.com/ng97taZ.gif">

## `C-g` - IDEA Escape

IDEA Escape used in different context, mostly meaning stop input or action.   
Stop selection, stop search, close popups, etc.   
Mnemonic is stop looking for "G" spot.

<style> 
.static-c-g { position:absolute; background: white; }
.static-c-g:hover { opacity:0; } 
</style>
<img class="bordered static-c-g" src="https://i.imgur.com/3eG16f9.gif">
<img class="bordered active-c-g" src="https://i.imgur.com/SrgY1DV.gif">
  
## `C-h` - delete character (backward, a.k.a Backspace)  

It was a hotkey for backspace in emacs, don't know why...   
In our case it's close to `J` key, which represent left arrow, so instead of moving cursor left, 
you slide index finger to the left, meaning, move cursor and delete the char. 

<style> 
.static-c-h { position:absolute; background: white; }
.static-c-h:hover { opacity:0; } 
</style>
<img class="bordered static-c-h" src="https://i.imgur.com/ld8HJuY.gif">
<img class="bordered active-c-h" src="https://i.imgur.com/GPudaF4.gif">

## `C-i`/`C-j`/`C-k`/`C-l` - arrows up/left/down/right

Look at the keyboard, how nicely ijkl combination represent the arrows, like wasd for gamers.

<style> 
.static-c-ijkl { position:absolute; background: white; }
.static-c-ijkl:hover { opacity:0; } 
</style>
<img class="bordered static-c-ijkl" src="https://i.imgur.com/oUYMZp6.gif">
<img class="bordered active-c-ijkl" src="https://i.imgur.com/btmT7o4.gif">
  
## `C-m`/`C-n` - enter or return

In home row position your right index finger on J button, 
just slide a little down and you will get to M. Or N. Works for both cases.

<style> 
.static-c-mn { position:absolute; background: white; }
.static-c-mn:hover { opacity:0; } 
</style>
<img class="bordered static-c-mn" src="https://i.imgur.com/4DmEQxM.gif">
<img class="bordered active-c-mn" src="https://i.imgur.com/TQpw72H.gif">

## `C-r` - replace in current file

"R" for replace. Also note, that while replace top dialog is available you have additional contextual hotkeys
- `C-m` - (enter) replace occurence and go to next  
- `C-k` - (arrow down) skip and go to next occurence    
- `C-i` - (arrow up) skip and go to previous occurence
- `C-g` - (escape) exit from replace dialog

<style> 
.static-c-r { position:absolute; background: white; }
.static-c-r:hover { opacity:0; } 
</style>
<img width="600px" class="bordered static-c-r" src="https://i.imgur.com/d8dSM1b.gif">
<img width="600px" class="bordered active-c-r" src="https://i.imgur.com/VzTNyIw.gif">

## `C-s` - search in current file

"S" for Search (and Solr). Similar to replace dialog, some contextual hotkeys available  

- `C-m` or `C-k` - (enter or arrow down) go to next occurence
- `C-i` - (arrow up) skip and go to previous occurence   
- `C-g` - (escape) exit from replace dialog

<style> 
.static-c-s { position:absolute; background: white;}
.static-c-s:hover { opacity:0; } 
</style>
<img width="600px" class="bordered static-c-s" src="https://i.imgur.com/wWod4Cy.gif">
<img width="600px" class="bordered active-c-s" src="https://i.imgur.com/18i5fLf.gif">

## `C-t` - prefix for Tool Mode

## `C-w` - cut

Like `C-x` for normal people. I don't how what mnemonic here, just used it since emacs times.  
Maybe, "W" looks like a saw. Or two pairs of scissors.

## `C-x` - prefix for Execute mode  

## `C-y` - paste

Like `C-v` for normal people. "Y" stands for "yanking" in emacs. Don't know what it means.

## `C-z` - undo

Good old `C-z`. It's like time machine, if everything is fucked up, you press it to go back in time. 

## `C-=` - extend selection

`=` located on the same key, where `+` is. Plus means to enlarge something.     
Smart selection is my #1 used feature in IDEA.

<style> 
.static-c-plus { position:absolute; background: white;}
.static-c-plus:hover { opacity:0; } 
</style>
<img class="bordered static-c-plus" src="https://i.imgur.com/lL10wS0.gif">
<img class="bordered active-c-plus" src="https://i.imgur.com/NzNK0zV.gif">

## `C--` - shrink selection

`-` is an opposite to `+`, so if there is a way to enlarge something, should be an option to shrink it.  
Not as popular as extend selection, but if you did one extra selection, there is a way to go back, 
rather than starting from beginning.

<style> 
.static-c-minus { position:absolute; background: white;}
.static-c-minus:hover { opacity:0; } 
</style>
<img class="bordered static-c-minus" src="https://i.imgur.com/yFJWWID.gif">
<img class="bordered active-c-minus" src="https://i.imgur.com/TGrMcih.gif">

## `C-] / C-[` - escape

IDEA has it's own escape `C-g` but it doesn't work for all cases.   
You still need real global escape. And better if you can touch it.

## `C-.` - completion

For me as for _java-programmer-in-the-past_, `.` has a special meaning - access a members of object.
IDEA often in this case automatically trigger code completion. So yes, `.` is for completion.

<style> 
.static-c-dot { position:absolute; background: white;}
.static-c-dot:hover { opacity:0; } 
</style>
<img class="bordered static-c-dot" src="https://i.imgur.com/qonMkx4.gif">
<img class="bordered active-c-dot" src="https://i.imgur.com/GDYjuPx.gif">

## `C-SPACE` - start/end sticky selection

Beloved feature from emacs. You don't need holding `SHIFT` to control selection, 
just activate sticky selection and move to desired state with arrows.

<style> 
.static-c-space { position:absolute; background: white;}
.static-c-space:hover { opacity:0; } 
</style>
<img class="bordered static-c-space" src="https://i.imgur.com/nx9hPW6.gif">
<img class="bordered active-c-space" src="https://i.imgur.com/ctmLPc9.gif">

# Alternative Keys (C-M-)

This category slightly changes the functionality of basic hotkeys.  
Hold both Control and Meta key (Command for MacOS) and you have new set of keybindings.  
Semantic meaning of such hotkeys stays the same, but adds some features or works in a different context.

## `C-M-a` - move cursor to the start of the file

`C-a` move cursor to start of the line, with meta it moves to the start of the file.

<style> 
.static-c-m-a { position:absolute; background: white; }
.static-c-m-a:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-a" src="https://i.imgur.com/dDQ0pFB.gif">
<img class="bordered active-c-m-a" src="https://i.imgur.com/JeGzqj2.gif"> 

## `C-M-b` - move cursor one function backward 

`C-b` move cursor one word backward, with meta it moves one function backward.

<style> 
.static-c-m-b { position:absolute; background: white; }
.static-c-m-b:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-b" src="https://i.imgur.com/nH8BMkB.gif">
<img class="bordered active-c-m-b" src="https://i.imgur.com/Vm51Rhr.gif"> 

## `C-M-d` - delete line from cursor to the end of line

`C-d` delete one character backward, with meta it deletes the rest of the line in the same direction.  
Note: for most programs (including terminal) this functionality available with hotkey `C-k`, 
but `C-k` is global arrow down for us. We resolved this conflict by global remapping `C-M-d` to `C-k`. 
You can use `C-M-d` across all applications which support `C-k`.

<style> 
.static-c-m-d { position:absolute; background: white; }
.static-c-m-d:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-d" src="https://i.imgur.com/sOsXeas.gif">
<img class="bordered active-c-m-d" src="https://i.imgur.com/fboZ30Q.gif"> 


## `C-M-e` - move cursor to the end of the file

`C-e` move cursor to end of the line, with meta it moves to the end of the file.

<style> 
.static-c-m-e { position:absolute; background: white; }
.static-c-m-e:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-e" src="https://i.imgur.com/v7hhtLp.gif">
<img class="bordered active-c-m-e" src="https://i.imgur.com/Wv5HcPl.gif"> 

## `C-M-f` - move cursor one function forward 

`C-f` move cursor one word forward, with meta it moves one function forward.

<style> 
.static-c-m-f { position:absolute; background: white; }
.static-c-m-f:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-f" src="https://i.imgur.com/4JHMm0Q.gif">
<img class="bordered active-c-m-f" src="https://i.imgur.com/LMsSw87.gif">

## `C-M-h` - delete line from the cursor to the start of line

`C-h` delete one character forward, with meta it deletes the rest of the line in the same direction.

<style> 
.static-c-m-h { position:absolute; background: white; }
.static-c-m-h:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-h" src="https://i.imgur.com/SWxEnEp.gif">
<img class="bordered active-c-m-h" src="https://i.imgur.com/Ys98ixw.gif"> 

## `C-M-i`/`C-M-j`/`C-M-k`/`C-M-l` - text movement

`C-ijkl` move cursor in four direction, with meta it moves line or selection in four directions.
Left/right directions works like a Tab/Shift-Tab (indent/unindent)

<style> 
.static-c-m-ijkl { position:absolute; background: white; }
.static-c-m-ijkl:hover { opacity:0; } 
</style>
<img class="bordered static-c-m-ijkl" src="https://i.imgur.com/fXaSAAF.gif">
<img class="bordered active-c-m-ijkl" src="https://i.imgur.com/Xkyr9fU.gif"> 

## `C-M-s` - search globally

`C-s` search for occurrence in current file, with meta it search across whole project/module.

<style> 
.static-c-m-s { position:absolute; background: white; }
.static-c-m-s:hover { opacity:0; } 
</style>
<img width="600px" class="bordered static-c-m-s" src="https://i.imgur.com/5UZbGu4.gif">
<img width="600px" class="bordered active-c-m-s" src="https://i.imgur.com/H5XnJJ1.gif"> 

## `C-M-z` - redo

`C-z` undo the action, with meta you can redo it it.

## `C-M-[`/`C-M-]` - escape from tool window and close it

One usage of escape button is to switch from the tool window back to editor. Tool window stil remains visible. 
If you want to hide tool window press extra meta key (will be covered more in Tool Mode section) 

# Code Mode (C-c prefix)

## `C-c a` - git annotate

## `C-c b` - go to mathcing brace

## `C-c c` - comment block

## `C-c d` - duplicate line

## `C-c e` 

## `C-c f` 

## `C-c g`
 
## `C-c h` 

## `C-c i` - indent code 

## `C-c j` 

## `C-c k` 

## `C-c l` - go to line

## `C-c m` - list methods 

## `C-c n` 

## `C-c o` 

## `C-c p` 

## `C-c q` 

## `C-c r` 

## `C-c s` 

## `C-c t` 

## `C-c u` 

## `C-c v` 

## `C-c w` 

## `C-c x` 

## `C-c y` 

## `C-c z` 

# Popular keybindings (M-)

## `M-a` - FREE
## `M-e` - Recently edited files
## `M-r` - REPL
## `M-j` - Jump

# Execute mode (C-x prefix)

## `C-x a`
## `C-x b`
## `C-x c`
 
# Tools mode (C-t prefix)
