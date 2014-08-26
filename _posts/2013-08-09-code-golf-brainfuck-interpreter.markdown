---
layout: post
title: "Code Golf: Brainfuck Interpreter"
date: 2013-08-09 17:44
comments: true
sharing: true
categories: [programming, clojure, code golf]
published: true
---

Brainfuck Interpreter in two tweets.

<!-- more -->

Previous article [Code Golf: Game of Life](/blog/code-golf-game-of-life/) raised some interest, and I decided to proceed. Today's problem is a Brainfuck Interpreter.

[Brainfuck](http://en.wikipedia.org/wiki/Brainfuck) is an esoteric programming language, famous because of its small command set. It is based on array of cells (Turing Tape) and pointer to this array. There are only 8 commands: 

* `>` move to the next cell
* `<` move to the previous cell
* `+` increment value in the current cell
* `-` decrement value in the current cell
* `.` print char with ascii value of current cell
* `,` read ascii value for input char to the current cell
* `[` start loop until value of pointer is not zero 
* `]` finish loop

That's all. Brainfuck is [Turing Complete](http://en.wikipedia.org/wiki/Turing_completeness) language, that means it capable to implement any program. If you crazy, of course.

### Tweet

Final version took `280` characters in Clojure:

``` clojure
(fn[a p k c](let[h #(nth %1@%2)e #(h a p)s #(swap! %1%2 1)t
#(aset a@p(%(e)1))l #(do(s k %)(case(h c k)\]()\[()(recur %)
))](while(>(count c)@k)(do(case(h c k)\>(s p +)\<(s p -)\+
(t +)\-(t -)\.(print(char(e)))\,(aset a@p(.read *in*))\[(if
(=(e)0)(l +))\](if(>(e)0)(l -)))(s k +)))))
```

Exactly 2 tweets.

### "Sugared" version

Translating to more readable code:

``` clojure
(defn parse-internal [a pt pc cs]
  (letfn [(act []
            (case (nth cs @pc)
              \> (swap! pt inc)
              \< (swap! pt dec)
              \+ (aset a @pt (inc (nth a @pt))) 
              \- (aset a @pt (dec (nth a @pt)))
              \. (print (char (nth a @pt)))
              \, (aset a @pt (.read *in*))
              \[ (if (zero? (nth a @pt)) (loop- inc))
              \] (if-not (zero? (nth a @pt)) (loop- dec))))
          (loop- [f]
            (do (swap! pc f)
                (case (nth cs @pc)
                  \[ ()
                  \] () 
                  (recur f))))]
    (while (not= (count cs) @pc)
      (do
        (act)
        (swap! pc inc)))))
```

So, what's happening there?

Function arguments are parameters of our tape and brainfuck program.

* `a` is an array represents finite tape `e.g (int-array 100)`
* `pt` is an atom - pointer to the tape
* `pc` is an atom - pointer to the command list
* `cs` command list

Function `act` decides which action to perform depending on current command,
`loop` allows us to move command pointer inside a loop,
and main `while-do` loop executes commands until they exhausted. Simple enough.

To make our interpreter more friendly we create function `parse` that accepts
string - program, written in brainfuck.

``` clojure
(defn parse [s]
  (let [a (int-array 100)  ;; Turing Tape
        p (atom 0)         ;; Pointer to Tape
        k (atom 0)         ;; Pointer to Command
        c (vec (seq s))]   ;; Vector of Commands
    (parse-internal a p k c)))
```

#### Testing

Print "Hello, world"

``` clojure
(parse "++++++++++[>+++++++>++++++++++>+++>+<<<<-]
        >++.>+.+++++++..+++.>++.<<+++++++++++++++.>
        .+++.------.--------.>+.>.") =>
Hello World!
```

Input 5 characters and reverse print them

``` clojure
(parse ",>,>,>,>,.<.<.<.<.") => <wait input "hello">
olleh
```

More complex program need nested loops, which is not supported by
this version (*for the sake of small size!*)

History of implementation, nested loops and more available [here](https://github.com/mishadoff/prog-experiment/blob/master/clojure/brainfuck.clj)

**P.S.** This version is not "fully-featured" brainfuck interpreter.

* It does not work for inifinite tape
* Cell datatype is fixed and not assumed to be cyclic. For example, if cell type is unsigned byte `[0..255]`, then `255 + 1 != 0` and break interpreter
* Only unsigned types supported
* Nested loops are not allowed
* No error handling

But, you are welcome to improve it!
