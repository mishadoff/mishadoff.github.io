---
layout: post
title: "Mnemonics-based keymap for IntelliJ IDEA"
date: 2018-09-25 14:00
comments: true
categories: [programming, idea, emacs, clojure, cursive, productivity]
sharing: true
published: true
---

Emacs is great for editing text, IntelliJ IDEA is great for navigation.  
Let's take best from both worlds into one editor.

<!-- more -->

# Rationale

> If you can configure it, you will configure it  
>                       - Anonymous, about Linux  

Emacs is keyboard-driven editor, meaning all actions you can do without mouse or touchpad. It is a good part.
The bad part is it lacks rich navigation capabilities, integration with external tools, code aware completion, etc.
Well, you still can try hundred of plugins and configure exactly what you need, or try tens of prebuild emacs-based
distributions with batteries included, but IntelliJ IDEA just works out of the box.

IDEA has most things that missing in Emacs and still considered among many as the best IDE for Java.  
Even more, [Cursive](https://cursive-ide.com) extension for Clojure works really well.

The most annoying things in IDEA is an editor and interaction with tool windows, I mean _you need mouse_. Even using emacs-based keymap, you will click mouse/touchpad extensively. 
Your fingers will be screaming. Especially, if you work on the latest MacBook Pro.

This is an attempt to develop mnemonics-based keymap (a.k.a Emacs style) for IntelliJ, 
to reduce mouse interaction to minimum, while providing a simple way to remember the hotkeys.

# Preparation

I'm a _happy_ user of MacBook Pro 13'' (2017). 
Really, I find MacOS still the best system for people and devs, but this new keyboard drive me crazy.

<img width="700px" src="https://i.imgur.com/GZLkuCpl.png">

Ironically, `Escape` button escaped to virtual world, so you can't get tactile feedback from it.
Apple provided a discount, so you got four arrows in the ~~price~~ place of three.
Greedy backspace, which steal all your home row attention if you want just delete a char. 
Small vertical enter.

I can continue ranting about non-consistent sound from different keypresses, 
but thanks to [this blog](http://tonsky.me/blog/cursor-keys/)
I remapped most of this keys, and happy so far.

Shortly, CapsLock remapped to Control (do you know people who use CapsLock?), 
then Control-IJKL become normal arrows, Control-H as a backspace (hello Emacs users), 
Control-M and Control-N work in pair as a horizontal Enter, and the same way Control-[ and Control-] work like Escape.
Also, I don't use right Command, so it also became Control, now you can use arrows with one hand while... eating or so.     

This may sound strange configuration, but for me it works like a charm.  
It took approximately two weeks to get used to new keyboard mapping.  
Below, I list all hotkeys. 

<style>.bordered { border: 1px grey solid; }</style>

# Basic hotkeys (C- prefix)

Basic hotkeys triggered with Control prefix. (`C-a` means press "A" while holding Control key)  
This category exposes basic keybindings for navigating and editing text.

## `C-a` - move cursor to the start of line

"A" is always beginning of something, line in this case.

<img class="bordered" src="https://i.imgur.com/42M6Fp1.gif"> 

## `C-b` - move cursor one word backward

"B" for backward. For long sentences it's faster than arrows.  

<img class="bordered" src="https://i.imgur.com/ZyW49TT.gif">

## `C-c` - prefix, reserved for [Code mode](#code-mode-c-c-prefix)
  
## `C-d` - delete character (forward)

"D" for delete. Because, my MacBook keyboard doesn't have delete button, only backspace.

<img class="bordered" src="https://i.imgur.com/e5JLMNA.gif">
    
## `C-e` - move cursor to the end of line

"E" for end. Opposite to "A".

<img class="bordered" src="https://i.imgur.com/zVt6P0D.gif">
  
## `C-f` - move cursor one word forward  

"F" for forward. Opposite to "B".

<img class="bordered" src="https://i.imgur.com/ng97taZ.gif">

## `C-g` - IDEA Escape

IDEA Escape used in different context, mostly meaning stop input or action.   
Stop selection, stop search, close popups, etc.   
Mnemonic is stop looking for "G" spot.

<img class="bordered" src="https://i.imgur.com/SrgY1DV.gif">
  
## `C-h` - delete character (backward, a.k.a Backspace)  

It was a hotkey for backspace in emacs, don't know why...   
In our case it's close to `J` key, which represent left arrow, so instead of moving cursor left, 
you slide index finger to the left, meaning, move cursor and delete the char. 

<img class="bordered" src="https://i.imgur.com/GPudaF4.gif">

## `C-i`/`C-j`/`C-k`/`C-l` - arrows up/left/down/right

Look at the keyboard, how nicely ijkl combination represent the arrows, like wasd for gamers.  
And almost like hjkl for vimers.

<img class="bordered" src="https://i.imgur.com/btmT7o4.gif">
  
## `C-m`/`C-n` - enter or return

In home row position your right index finger on J button, 
just slide a little down and you will get to M. Or N. Works for both cases.

<img class="bordered" src="https://i.imgur.com/TQpw72H.gif">

## `C-r` - replace in current file

"R" for replace. Also note, that while replace top dialog is available you have additional contextual hotkeys
- `C-m` - (enter) replace occurence and go to next  
- `C-k` - (arrow down) skip and go to next occurence    
- `C-i` - (arrow up) skip and go to previous occurence
- `C-g` - (escape) exit from replace dialog

<img width="700px" class="bordered" src="https://i.imgur.com/VzTNyIw.gif">

## `C-s` - search in current file

"S" for Search (and Solr). Similar to replace dialog, some contextual hotkeys available  

- `C-m` or `C-k` - (enter or arrow down) go to next occurence
- `C-i` - (arrow up) skip and go to previous occurence   
- `C-g` - (escape) exit from replace dialog

<img width="700px" class="bordered" src="https://i.imgur.com/18i5fLf.gif">

## `C-t` - prefix for [Tool Mode](#tool-mode-c-t-prefix)

## `C-w` - cut

Like `C-x` for normal people. I don't how what mnemonic here, just used it since emacs times.  
Maybe, "W" looks like a saw. Or two pairs of scissors.

## `C-x` - prefix for [Execute mode](#execute-mode-c-x-prefix)

## `C-y` - paste

Like `C-v` for normal people. "Y" stands for "yanking" in emacs. Don't know what it means.

## `C-z` - undo

Good old `C-z`. It's like time machine, if everything is fucked up, you press it to go back in time. 

## `C-=` - extend selection

`=` located on the same key, where `+` is. Plus means to enlarge something.     
Smart selection is my #1 used feature in IDEA.

<img class="bordered" src="https://i.imgur.com/NzNK0zV.gif">

## `C--` - shrink selection

`-` is an opposite to `+`, so if there is a way to enlarge something, should be an option to shrink it.  
Not as popular as extend selection, but if you did one extra selection, there is a way to go back, 
rather than starting from beginning.

<img class="bordered" src="https://i.imgur.com/TGrMcih.gif">

## `C-] / C-[` - escape

IDEA has it's own escape `C-g` but it doesn't work for all cases.   
You still need real global escape. And better if you can touch it.

## `C-.` - completion

For me as for _java-programmer-in-the-past_, `.` has a special meaning - access members of object.
IDEA often in this case automatically trigger code completion. So yes, `.` is for completion.

<img class="bordered" src="https://i.imgur.com/GDYjuPx.gif">

## `C-SPACE` - start/end sticky selection

<img class="bordered" src="https://i.imgur.com/ctmLPc9.gif">

# Alternative Keys (C-M-)

This category slightly changes the functionality of basic hotkeys.  
Hold both Control and Meta key (Command for MacOS) and you have new set of keybindings.  
Semantic meaning of such hotkeys stays the same, but adds some features or works in a different context.

## `C-M-a` - move cursor to the start of the file

`C-a` move cursor to start of the line, with meta it moves to the start of the file.

<img class="bordered" src="https://i.imgur.com/JeGzqj2.gif"> 

## `C-M-b` - move cursor one function backward 

`C-b` move cursor one word backward, with meta it moves one function backward.

<img class="bordered" src="https://i.imgur.com/Vm51Rhr.gif"> 

## `C-M-d` - delete line from cursor to the end of line

`C-d` delete one character backward, with meta it deletes the rest of the line in the same direction.  
Note: for most programs (including terminal) this functionality available with hotkey `C-k`, 
but `C-k` is global arrow down for us. We resolved this conflict by global remapping `C-M-d` to `C-k`. 
You can use `C-M-d` across all applications which support `C-k`.

<img class="bordered" src="https://i.imgur.com/fboZ30Q.gif"> 


## `C-M-e` - move cursor to the end of the file

`C-e` move cursor to end of the line, with meta it moves to the end of the file.

<img class="bordered" src="https://i.imgur.com/Wv5HcPl.gif"> 

## `C-M-f` - move cursor one function forward 

`C-f` move cursor one word forward, with meta it moves one function forward.

<img class="bordered" src="https://i.imgur.com/LMsSw87.gif">

## `C-M-h` - delete line from the cursor to the start of line

`C-h` delete one character forward, with meta it deletes the rest of the line in the same direction.  
Never used.

<img class="bordered" src="https://i.imgur.com/Ys98ixw.gif"> 

## `C-M-i`/`C-M-j`/`C-M-k`/`C-M-l` - text movement

`C-ijkl` move cursor in four direction, with meta it moves line or selection in four directions.
Left/right directions works like a Tab/Shift-Tab (indent/unindent)

<img class="bordered" src="https://i.imgur.com/Xkyr9fU.gif"> 

## `C-M-m`/`C-M-n` - alternative enter

`C-m`/`C-n` powered by Karabiner and replace default enter fucntionality. 

Alternative enter available in Idea in various context. For example, pressing enter on project view file, open this file in editor, but doesn't switch to it. Alternative enter switch to the file. Also, pressing enter on unfinished input in REPL, adds newline. If you press alternative enter instead, whole form will be sent for evaluation.

## `C-M-r` - replace globally

`C-r` replace occurrences in current file, with meta it replace across whole project/module. While in this dialog use arrows to navigate through occurences and press `Opt-R` ro replace.  (Don't like `Opt-R`, but there is no way to override it in Idea)

<img width="700px" class="bordered" src="https://i.imgur.com/9RDkbiX.gif"> 


## `C-M-s` - search globally

`C-s` search for occurrence in current file, with meta it search across whole project/module.

<img width="700px" class="bordered" src="https://i.imgur.com/H5XnJJ1.gif"> 

## `C-M-z` - redo

`C-z` undo the action, with meta you can redo it it.

## `C-M-[`/`C-M-]` - escape from tool window and close it

One usage of escape button is to switch from the tool window back to editor. Tool window stil remains visible. 
If you want to hide tool window press extra meta key (will be covered more in Tool Mode section) 

# Code Mode (C-c prefix)

Code Mode provide hotkeys (prefixed with `C-c`) to work with the code.

## `C-c a` - git annotate

When you find shitty piece of code, you want to know who added it. Blame him.  
Idea call it A - Annotate.

<img width="700px" class="bordered" src="https://i.imgur.com/MK5phRh.gif"> 

## `C-c b` - move cursor to matching brace

B for Brace. Navigating to matching brace helps you to add something before or after form.  
Also, nice to see your parens are balanced.

<img width="700px" class="bordered" src="https://i.imgur.com/hYUQaVx.gif"> 


## `C-c c` - comment

Most of the time is simpler to comment by adding `;` in front of the line, or `#_` if you want to comment whole form. But when cursor is somewhere in the middle of line, this hotkey, comment the line and jumps to the next one. Handy. C obviously for comment.

<img width="700px" class="bordered" src="https://i.imgur.com/jLeFCmU.gif"> 

## `C-c d` - duplicate line

DRY principle stands for Don't Repeat Yourself. I doubt it is true for developers, copy/paste it's all we have.  
D for Duplicate or Do Repeat Yourself.

<img width="700px" class="bordered" src="https://i.imgur.com/MRRpwD5.gif">

## `C-c e` - jump to next error

Programming without errors is not a programming. Jump to next error. 

<img width="700px" class="bordered" src="https://i.imgur.com/U6MfZcs.gif">

## `C-c C-e` - error description

After you jumped to error, you need to get description of what's wrong. 
Usually people do this by hovering mouse over the error, but we have a keyboard.

<img width="700px" class="bordered" src="https://i.imgur.com/ZulIDEN.gif">

## `C-c f` - find current file in project view 

To see context of directory or package you are working in, reveal the file in project view. 

<img width="700px" class="bordered" src="https://i.imgur.com/kHRFYWJ.gif">

## `C-c h` - inline help

Call inline documentation of the function, if you forgot the order of arguments or want to make sure it does exactly what you need. H for Help. Escape closes popup.

<img width="700px" class="bordered" src="https://i.imgur.com/fv32zaM.gif">

## `C-c i` - indent code 

Copypasted code from somewhere else and got broken indentation? Press "I" to indent.

<img width="700px" class="bordered" src="https://i.imgur.com/KcRgVk5.gif">

## `C-c j` - ace jump

Jump. It's a special keybinding prefix powered by [acejump plugin](https://plugins.jetbrains.com/plugin/7086-acejump). Type this prefix, type some char and acejump will highlight all occurences of this char in the visible editor. Use hints to move to specific place.  
Poor man's replacement for the mouse "click here".

<img width="700px" class="bordered" src="https://i.imgur.com/cHec1ju.gif">

## `C-c k` - kill whole line

Kill current line, no matter where you at.

<img width="700px" class="bordered" src="https://i.imgur.com/CUdKoDZ.gif">

## `C-c l` - go to line

Move to the line. Generally used if you got an exception with extra on what line error occurs.

<img width="700px" class="bordered" src="https://i.imgur.com/wBOoeqE.gif">

## `C-c m` - list methods 

Correct name for this command is "show file structure", but you mostly working with the code, so file structure are methods and variables. M methods. 

<img width="700px" class="bordered" src="https://i.imgur.com/s1JAajd.gif">

## `C-c C-m`/`C-c C-n` - IDEA show intentions

Ideal way to perform imports, requires and other contextual suggestions.  
Mnemonic is "Code (C-c) Enter (C-m, C-n)".

<img width="700px" class="bordered" src="https://i.imgur.com/3ebJUb4.gif">

## `C-c r` - refactor menu

R for refactor, very powerful IDEA feature, but limited in clojure. 
Hopefully, could be improved to contain more refactoring items.

<img width="700px" class="bordered" src="https://i.imgur.com/M6vDjUm.gif">

## `C-c s` - go to source

Exciting code navigating feature. Press "S" (source), to go to the function definition, either current project or external library.

<img width="700px" class="bordered" src="https://i.imgur.com/M6AhSo0.gif">

## `C-c t` - transpose forms

If you misplaced the arguments to the function, 
press "T" (transpose) to swap them. Works on forms as well, current form will be swapped with the left.

<img width="700px" class="bordered" src="https://i.imgur.com/peRF9tq.gif">

## `C-c u` - find usages

U for usages. Prior to changing function or its implementation you want to know
what pieces it affects. Must have feature for any editor.

<img width="700px" class="bordered" src="https://i.imgur.com/GXAIOZv.gif">

## `C-c w` - wrap selection

Select piece of code, and wrap it to something. `try-catch` block is the most popular choice, but you can add your own snippets.

<img width="700px" class="bordered" src="https://i.imgur.com/oz2t4N5.gif">

## `C-c -` - collapse

Long function can be collapsed into oneliner. 
Minus sign means reduce visible part of text. 

<img width="700px" class="bordered" src="https://i.imgur.com/v2P8HQW.gif">

## `C-c =` - expand

Collapsed function, can be expanded to normal view again. 
As already mentioned, `=` sign located on the button with `+`, which means enlarge.

<img width="700px" class="bordered" src="https://i.imgur.com/wFnxu3U.gif">

## `C-c C--` - collapse all

Alternatively to collapse, there is posibility to collapse all functions. 
Hide all, open one and focus on it. But if your namespace overloaded with a lot of functions think about better way to focus on one function.

<img width="700px" class="bordered" src="https://i.imgur.com/0w1zqOS.gif">

## `C-c C-=` - expand all

Similarly, there is expand all keyboard, bring everything to normal.

<img width="700px" class="bordered" src="https://i.imgur.com/U6RBjkj.gif">

# Execute mode (C-x prefix)

Execute mode provide hotkeys to interact with REPL and editor window.

## `C-x b` - set bookmark

Save frequently-visited location in the code to bookmark.

<img width="700px" class="bordered" src="https://i.imgur.com/whvnTZS.gif">

## `C-x C-b` - show bookmarks

Show all saved bookmarks and navigate/edit them.

<img width="700px" class="bordered" src="https://i.imgur.com/ZZciL2y.gif">

## `C-x d` - show debug menu

D for Debug. But remember, best debug are print statements.

<img width="700px" class="bordered" src="https://i.imgur.com/5t6hIdk.gif">

## `C-x e` - evaluate form in REPL

Eval is most important hotkey for REPL driven development.  
So important so `C-x C-e` does the same.

<img width="700px" class="bordered" src="https://i.imgur.com/ElOgz8F.gif">

## `C-x f` - go to file

Old school emacs hotkey to open file, `C-x C-f` does the same.  

<img width="700px" class="bordered" src="https://i.imgur.com/UbZ9jBM.gif">

## `C-x g` - show git dialog

Opens git dialog. Not keyboard friendly and not as powerful as [magit](https://github.com/magit/magit), but just in case you use IntelliJ git client. 
I am totally fine with console [tig](https://github.com/jonas/tig) tool, though simple magit port for IDEA would be good.

<img width="700px" class="bordered" src="https://i.imgur.com/Q0RO0Yc.gif">

## `C-x C-h` - Clear REPL output

`C-h` is backspace in our world, and backspace means to clear.

<img width="700px" class="bordered" src="https://i.imgur.com/wx95Bol.gif">

## `C-x i` - interrupt

If function runs too long, press I to interrupt.

<img width="700px" class="bordered" src="https://i.imgur.com/sQRMBF8.gif">

## `C-x k` - kill window

Kill editor window, when it's not needed anymore.

<img width="700px" class="bordered" src="https://i.imgur.com/QbpYFyN.gif">

## `C-x l` - load file in REPL

Load file in REPL. Sync all forms between file and REPL environment.  
Very important file of the flow: change file -> load to REPL -> repeat

<img width="700px" class="bordered" src="https://i.imgur.com/2QMqpfH.gif">

## `C-x m` - start/stop macro recording

Not as powerful macro capabilities as in emacs, 
but still there is a way to record routine actions.  
The same hotkeys start and stops macro recording. 
After macro is recorded, you can provide a name for it, to make it persistent.  
`M-m` plays the last macro.

<img width="700px" class="bordered" src="https://i.imgur.com/OH7VSMr.gif">

## `C-x n` - set current file to namespace in REPL

Working in `user` namespace is often error-prone, when loaded function names mixed in one environment.  
Use namespaces for REPL instead. The same as `(in-ns)`

<img width="700px" class="bordered" src="https://i.imgur.com/DR8OCLU.gif">

## `C-x o` - go to next split window

Emacs allows to open and arrange several buffers in one screen. Similarly,
IDEA allows to view multiple editors at once, `C-x o` cycles to next visible editor.
I always working in one editor, sometimes open two to copy chunks or compare files.
Therefore, O means other.

<img width="700px" class="bordered" src="https://i.imgur.com/PebeFrK.gif">

## `C-x C-o` - Open file...

IDEA is project-based editor, but there is a way to open external files for quick edit. 
Don't know why.

<img width="700px" class="bordered" src="https://i.imgur.com/K5HpMDI.gif">

## `C-x q` - stop REPL

Sometimes, you will need to restart REPL to refresh dependenies, cleanup environment, etc.  
Just quit current REPL and start new one using `C-x x`.

<img width="700px" class="bordered" src="https://i.imgur.com/IivfiXT.gif">

## `C-x s` - search REPL history

Just search.

<img width="700px" class="bordered" src="https://i.imgur.com/IRrfrvt.gif">

## `C-x t` - Run test under cursor

TDD adepts will love that.

<img width="700px" class="bordered" src="https://i.imgur.com/okukWlk.gif">

## `C-x C-t` - Run all tests in current namespace

When feature is done, run all tests for it.

<img width="700px" class="bordered" src="https://i.imgur.com/56FlGwr.gif">

## `C-x C-j` - navigate backward

Go through history of files and places which has been edited.  
`C-j` is a left arrow, remember? 

<img width="700px" class="bordered" src="https://i.imgur.com/5KShw4S.gif">

## `C-x C-l` - navigate forward

Similarly, go forward through history of edited files.

<img width="700px" class="bordered" src="https://i.imgur.com/n9uJhI8.gif">

## `C-x x` - show run menu

X for execute. Double X for Execute program using execute mode.

<img width="700px" class="bordered" src="https://i.imgur.com/1Abi0E2.gif">

## `C-x 0` - close current split editor

The same as `C-x k`, good to have consistency there. `0` means nothing.

<img width="700px" class="bordered" src="https://i.imgur.com/8lNUqOf.gif">

## `C-x 1` - close all split editors except current

Leave only current editor. `1` and only one.

<img width="700px" class="bordered" src="https://i.imgur.com/O4WbwJ1.gif">

## `C-x 2` - split editor verically

A way to add more editors to the view.

<img width="700px" class="bordered" src="https://i.imgur.com/C6LP83O.gif">

## `C-x 3` - split editor horizontally

Another way to add more editors to the view.

<img width="700px" class="bordered" src="https://i.imgur.com/JY7ugOg.gif">

## `C-x C-=` - Increase font size

Again, `=` located on the same button as `+`.

<img width="700px" class="bordered" src="https://i.imgur.com/baBtKTG.gif">

## `C-x C--` - Decrease font size

Decrease font size. `-` opposite to `+`.

<img width="700px" class="bordered" src="https://i.imgur.com/2nEpmdW.gif">

## `C-x C-0` - Set font size to default

Good to have default one.

<img width="700px" class="bordered" src="https://i.imgur.com/ZwAk0Zk.gif">

# Tool mode (C-t prefix)

Tool mode allows to manage IDEA tool windows.

## `C-t c` - open TERMINAL tool window

C for console. Generally not used, but when you switch often between terminal and edited file, could be handy.  

<img width="700px" class="bordered" src="https://i.imgur.com/VTTxrw0.gif">

## `C-t d` - open DEBUG tool window

IDEA Debug has a lot of features, when you debugging Java code, which could be another topic.  
I use sometimes `Opt-I` (step in), `Opt-O` (step-out), `Opt-N` (next statement) and `Opt-E` (valuate expression).
Because keys like F8 are completely meaningless (and not available in MacBook Pro)

<img width="700px" class="bordered" src="https://i.imgur.com/3wxWdan.gif">

## `C-t e` - open EVENT LOG tool window

Event log holds some information about IDEA system exceptions and compilation.  
Not really useful when you have REPL.

<img width="700px" class="bordered" src="https://i.imgur.com/eJNxt13.gif">

## `C-t f` - maximize tool window

F for Fullscreen.

<img width="700px" class="bordered" src="https://i.imgur.com/GHcZAyx.gif">

## `C-t g` - open GIT tool window

Another place for Git. Quick look at what has been edited.

<img width="700px" class="bordered" src="https://i.imgur.com/R1goX3P.gif">

## `C-t h` - Hide all windows (except editor)

H for Hide. Also `C-h` is backspace which has meaning delete, tool windows in this context.

<img width="700px" class="bordered" src="https://i.imgur.com/svUuX2Y.gif">

## `C-t C-i/C-j/C-k/C-l` - Adjust Size of tool window

Arrows naturally shows the direction in which tool window will be extended.

<img width="700px" class="bordered" src="https://i.imgur.com/BY56fVv.gif">

## `C-t l` - open LEININGEN tool window

L is lein. Shows dependency tree and tasks available. 
Also lein tasks could be run from this view, what some people find handy.  
I still prefer using lein from terminal.

<img width="700px" class="bordered" src="https://i.imgur.com/0pQSvEK.gif">

## `C-t m` - open MAVEN tool window

The same for Maven.

<img width="700px" class="bordered" src="https://i.imgur.com/lO3uFnM.gif">

## `C-t o` - open REPL OUTPUT tool window

Jump to the REPL output. 

<img width="700px" class="bordered" src="https://i.imgur.com/Nv6ual7.gif">

## `C-t p` - open PROJECT tool window

P. Open project view.

<img width="700px" class="bordered" src="https://i.imgur.com/9BHa4Mu.gif">

## `C-t q` - Quit tool window

Tool windows which are not needed anymore should be closed to free space.

<img width="700px" class="bordered" src="https://i.imgur.com/1OpDjjv.gif">

## `C-t r` - open REPL tool window

All you need is REPL. REPL is all you need.

<img width="700px" class="bordered" src="https://i.imgur.com/gsOzLAC.gif">

## `C-t s` - open SEARCH tool window

Back to search view, where find usages `C-c u` was submitted.

<img width="700px" class="bordered" src="https://i.imgur.com/zgJTfSM.gif">

## `C-t t` - open TODO tool window

Who else uses TODO list to organize their work?

<img width="700px" class="bordered" src="https://i.imgur.com/REGPVzu.gif">

## `C-t x` - open RUN tool window

Mostly used in Java. When you executed program, shows it's output.

<img width="700px" class="bordered" src="https://i.imgur.com/sfFkNSh.gif">

# Popular keybindings (M-)

Popular hotkeys has no specific topic, but due to its frequent usage they belong to one group.
`M-` does not require prefix, which means, they will be even faster than other hotkeys.

## `M-a` - select all

Select all is a frequent action, for example copy configuration from one file to another.

<img width="700px" class="bordered" src="https://i.imgur.com/hijaZrh.gif">

## `M-b` - Paredit Barf

Structural editing for Clojure, moves last form out of the current form.

<img width="700px" class="bordered" src="https://i.imgur.com/DBi02FL.gif">

## `M-c` - Copy

Just plain copy.

## `M-d` - Delete whitespaces

Delete whitespaces (including newlines). 
Like `C-d` but applied continuosly to the first non-whitespace symbol.

<img width="700px" class="bordered" src="https://i.imgur.com/LwlPUX6.gif">

## `M-e` - Recently edited files

Shows menu with recently edited files and tool windows.  
Poor replacement for emacs `ibuffer`.

<img width="700px" class="bordered" src="https://i.imgur.com/u1lKB6x.gif">

## `M-f` - Move forward into SEXP on the same level

Structural movement on the forms at one level. Kinda useful.

<img width="700px" class="bordered" src="https://i.imgur.com/Zus3nK4.gif">

## `M-i` - Interrupt evaluation

Already discussed, available at hotkey `C-x i`. 
I so often evaluate lazy sequence in REPL, so hotkey promoted to the popular ones.

## `M-j` - Jump

The same, available in code mode `C-c j`, but if used extensively extra prefix makes it slower.

## `M-k` - open symbol

If you remember name of the function or var, but forgot namespace it belongs to, use this.
`K` has no special meaning here, just available no prefix hotkey.

<img width="700px" class="bordered" src="https://i.imgur.com/2qEnkZv.gif">

## `M-l` - Load file into REPL

Promoted from `C-x l`

## `M-m` - Play last macro

We have already seen how you can record a macro using `C-x m`. Playback just applies it.
Unfortunately, there is no `C-u` modifier like in emacs to repeat macro n times. 
But you can press `M-m` n times.

## `M-n` - open namespace

Used mostly when working with clojure code, otherwise could be superceded by open file `C-x C-f`.

<img width="700px" class="bordered" src="https://i.imgur.com/bfq7ubX.gif">

## `M-o` - REPL output

Promoted from `C-t o`

## `M-p` - project view

The same as `C-t p`

## `M-r` - REPL

No questions why REPL is so popular, promoted `C-t r`.

## `M-s` - Paredit Slurp

Structural editing for Clojure. Awesome feature.

<img width="600px" class="bordered" src="https://i.imgur.com/auFYuEm.gif">

## `M-t` - Run Tests

Promoted from `C-x C-t`

## `M-u` - Uber menu

Open context menu. If some action can not be achieved using hotkey, 
you still have the context menu with all options available.

<img width="700px" class="bordered" src="https://i.imgur.com/l9tOnBB.gif">

## `M-v`/`M-w` - Paste

One from MacOS, one from emacs, so I added them both.

## `M-x` - Enter action

Honestly, there is no need to have a hotkey for every action. It makes sense to add hotkey if it has mnemonic, so you
do not spend extra cost remebering it, or its really popular. Otherwise all IDEA actions available to you under `M-x`, 
execute action.

<img width="700px" class="bordered" src="https://i.imgur.com/QXkEY1l.gif">

## `M-,` - IDEA Settings

Open IDEA settings. I doubt you can do something there without a mouse.

# Conclusion

I guess everyone has its own way of coding, favorite editor and set of plugins. 
That is fine, and I am not going to convince you this is better way of doing that yours.
All I wanted to share, that despite many years of clojure in emacs, 
I found IDEA with Cursive smart and very promising development environment for clojurists.
(_maybe, not so extensible_)

I still use emacs and cider sometimes for simple clojure projects, org-mode, 
markdown editor, notes, default terminal editor and ocassional development on other languages.

And lastly, in case if you interested in the same settings, [idea_keys.jar](/files/idea_keys.jar)
