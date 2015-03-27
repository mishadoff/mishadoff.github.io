---
layout: post
title: "Emacs: org mode"
date: 2015-03-27 14:30
comments: true
categories: [emacs]
sharing: true
published: true
---

Emacs [org-mode](http://orgmode.org/) use cases, for non-programmers.

<!-- more -->

# org-mode

Org-mode is what makes emacs popular among non-programmers community.
It could replace your Notes, TODO, Calendar, Agenda, Project Management, Time Tracking, Spreadsheets and other apps.

But seems like most of org-mode tutorials are dedicated to hardcore emacs users,
these tutorials are hard to follow as they are exposing all-the-power of org-mode.

Instead of drinking from the firehose, let's focus on some basic functionality, and you can incrementally improve it based on own needs. Just skip functionality you don't care.

**Prerequisites:** emacs novice  
**Keybindings:** based on my [configuration](https://github.com/mishadoff/.emacs.d/blob/master/lib/keyboard.el)

# Index

- [Notes](#notes)  
  - [New File](#notes_new_file)
  - [Markup](#notes_markup)
- [TODO](#todo)
	- [List](#todo_list)
	- [Nested Lists](#todo_nested_lists)
	- [Folding](#todo_folding)
	- [Search](#todo_search)
	- [Manage Nesting](#todo_manage_nesting)
	- [Parent](#todo_parent)
	- [Narrow](#todo_narrow)
	- [Delete](#todo_delete)
	- [Completion](#todo_completion)
	- [Description](#todo_description)
	- [Sparse Tree](#todo_sparse_tree)
	- [Statistics](#todo_statistics)
	- [Local References](#todo_local_refs)
	- [File References](#todo_file_refs)
	
# <div id="notes"/>Notes

### <div id="notes_new_file"/>New File

Create a plain text file in emacs and name it `notes.org`.
Emacs looks at `.org` extension and automatically enables `org-mode`  
This is where journey begins!

![](/images/org/notes_new_file.gif)

`C-x C-f` - create file  
`C-x C-s` - save file  

### <div id="notes_markup"/>Markup

Plain text is the best format ever. Both Human and Computers can read it.
Org-mode improves it by providing a simple markup syntax to
emphasize some points.

![](/images/org/notes_markup.gif)

# <div id="todo"/>TODO

### <div id="todo_list"/> List

Plain text is not well suited for planning, Lists are.
To make a list item in org mode, just put a `*` before entry title.

> Make a plan for birthday celebration.

![](/images/org/todo_list.gif)

### <div id="todo_nested_lists"/>Nested Lists

You can split each task into subtasks by adding a nested list.  
Just one more `*` introduce nested list.

> Add more details to plan

![](/images/org/todo_nested_lists.gif)

### <div id="todo_folding"/>Folding

So far, we have seen functionality available in every plain text editor, where is the power of org-mode?  
Press `TAB` on list item and it will collapse/expand all the subitems in a tree.

> Hide all details

![](/images/org/todo_folding.gif)

As you noticed in minibuffer tree different messages appear

- **FOLDED** fold all children and show only top-level item
- **CHILDREN** show current item and all its direct children
- **SUBTREE** expand whole tree for current item, includes deep nesting as well

### <div id="todo_search"/>Search

Even if your items are folded, you still have the power of search.

> Make sure pork is in the list.

![](/images/org/todo_search.gif)

`C-s` - search  

### <div id="todo_move_items"/>Move Items

Having tree of tasks it's easy to mimic priorities and move most important items to the top of list.  

> Call friends first, maybe they are busy and you will celebrate birthday alone

![](/images/org/todo_move_items.gif)

`C-S-up` - move subtree up  
`C-S-down` - move subtree down  

Have you noticed `C-up/down` is used to jump items with the same nesting level? 

### <div id="todo_manage_nesting"/>Manage Nesting

> Buying food and buying drinks are actually one activity - going to shop. Let's fix that.

![](/images/org/todo_manage_nesting.gif)

`M-RET` - create item with the same nesting level  
`M-right/left` - add/remove one nesting level for item  
`C-M-right/left` - add/remove one nesting level for subtree  

### <div id="todo_parent"/>Parent

Having lot of items and deep nesting makes easy to get lost.  
`M-<up>` is your Ariadne's thread.

![](/images/org/todo_parent.gif)

### <div id="todo_narrow"/>Narrow

Other approach to limit a visible part is narrowing.  

> Let's focus on syrups for now

![](/images/org/todo_narrow.gif)

`C-o n` - **n**arrow to subtree  
`C-o w` - **w**iden to whole file  

Note, there is a `Narrow` text indicates that you are in narrowed mode.

### <div id="todo_delete"/>Delete

Narrow mode is great feature to focus.
But the best way to focus is to cleanup obsolete items.  

> Review the tasks and clean obsolete

![](/images/org/todo_delete.gif)

`C-o k` - **k**ill subtree  

### <div id="todo_completion"/>Completion

The most important feature of todo lists is tracking completion.
Sure you can delete completed tasks, but how to report later?

Meet special markers `TODO`/`DONE`

> Review the tasks and mark what is TODO and what is already DONE

![](/images/org/todo_todo.gif)

`S-right` - cycle item through states {`EMPTY`, `TODO`, `DONE`}  

### <div id="todo_description"/>Description

Avoid long names for titles. Give title a self-contained short name
and provide detailed desription inside.

> Describe what tickets section mean

![](/images/org/todo_description.gif)

You still can navigate items, skipping description.  
`M-down` - go to next visible item

### <div id="todo_sparse_tree"/>Sparse Tree

Sparse trees are used if you need find and highlight all occurences
of some item, and unfold tree for matching clauses.

> List all TODO items

![](/images/org/todo_sparse_tree.gif)

`C-o s` - **s**parse tree  
`C-o s t` - sparse tree of TODO items  
`C-o e` - next highlighted **e**lement  
`C-o r` - previous highlighted item  
`C-c C-c` - (sparse tree context) clear highlighting  

### <div id="todo_statistics"/>Statistics

Often you need to get a sense how many TODO items in subtree.  
Type `[/]` (total number of todo items) or `[%]` (completion percentage)
in the item heading. Stats are automatically updated if subitem status changes.

![](/images/org/todo_statistics.gif)

`C-c C-c` - (statisics context) force refresh statistics

If all subtasks are `DONE`, than the parent task considered `DONE` as well.

### <div id="todo_local_refs"/>Local References

Information can be in the same document but in another place.
Use local references.  

`<<ref>>` - create a reference  
`[[ref][Ref Title]]` - creates a link that follows to `ref`  

> Add responsible for tickets design

![](/images/org/todo_local_refs.gif)

### <div id="todo_file_refs"/>File References

If additional information is in another file, instead of copying content you
can refer it as well. Just another syntax.

`[[file:~/games.txt::27]]` - creates a link to external file `games.txt`, `line:22`  

> Provide links for games

![](/images/org/todo_file_refs.gif)


I plan to update this post later with exposing more advanced functionality of org-mode: item properties, estimates, project planning, calendar and spreadsheets.
If you don't want to miss an update [subscribe to rss](/rss/) or [follow me on twitter](https://twitter.com/mishadoff)
