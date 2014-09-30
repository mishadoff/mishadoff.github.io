---
layout: post
title: "ClojureCup 2014: Impressions"
date: 2014-09-31 00:30
comments: true
categories: [clojure, clojurecup, programming]
sharing: true
published: true
---

[Clojure Cup 2014](https://clojurecup.com) has finished and we've build clojure app in two days!  
We are proud to announce [funstructor](http://funstructor.clojurecup.com/) (*pre-alpha*)

<!-- more -->

# Funstructor

<img src="http://i.imgur.com/mxrVjTB.png" width="600" height="320" />

Funstructor is a turn-based multiplayer card game for clojurists.
Your goal is to make some clojure code snippet, function or expression, but instead
of typing characters you must use cards.

Every card has unique behaviour and I suggest actually play game,
instead of reading what this game is about.

[Tutorial](https://github.com/clojurecup2014/funstructor/blob/master/doc/help.md)
might help to understand the basics.

# split-brain team

```clojure
(sort-by count team)
```

- **Max** (frontend, javascript, design)
- **Dima** (frontend, backend, clojure, clojurescript, ops)
- **Oleg** (backend, clojure, core.async, websockets)
- **Misha** (backend, clojure, game logic, art)

# Good Parts

### In one place

We were working in one office, so it was easy for us to communicate and solve problems.
Hackathon is not solely about 48 hours coding, it is more about learning new things while having fun.
I suppose, sitting two days at home alone is not much fun, even if you are hacking Clojure.

### Protocols and API

At the start of the hackathon we've made some planning and designed protocols and API for communication.
Obviously, this API was improving regularly, but the main profit is we could work independently.
Even more, we had nothing interesting to show after first day of coding. At the next day, when we've connected our parts together, magic things happened. It worked.

Client was communicating with server via WebSockets by sending messages like
below

``` clojure
{:type :game-request
 :data {:player-name "Rich Hickey"}}

{:type :action
 :data {:player-name "Rich Hickey"
        :card-position 3
		:target :self
		:args [2]}}
```

And server was using game logic API with small set of functions

``` clojure
(make-game)
(end-turn-for-player)
(end-turn)
(use-card)
```

### REPL

Actually, all worked because of REPL. No unit-testing, just REPL. Write some small function, test it
locally on the game state, handle corner-cases, test again and commit.

## WebSocket

Websockets with [chord](https://github.com/james-henderson/chord)
and [core.async](https://github.com/clojure/core.async) as easy as pie.

``` clojure
(defn ws-handler [{:keys [ws-channel] :as req}]
  (go-loop []
    (let [{:keys [message error] :as msg}
	      (async/<! ws-channel)] ;; reading from ws channel
	  (process-message message)
      (recur))))

(defroutes app-routes
  (GET "/ws" []
    (-> ws-handler
        (wrap-websocket-handler {:format :str
                                 :read-ch (chan)
                                 :write-ch (chan)}))))
```

### State? You're doing it wrong

Another good part that game logic was coded without atoms and refs.
We have map that represent game state and we have bunch of pure functions
which make some operations and return new map.

Server code that make update to atom, uses `swap!` in one place. What can be better?

Looks like good thought is to replace global state with flow that core.async provides. Instead of `swap!`ping our global atom with new updated state, server side can be decomposed into several processes:

- **handshake-process**: accepts websocket channel and waits for game-request. When player requests for game it sends websocket and newly created player id to **pending-checker-process**
- **pending-checker-process**: accumulates all pending players and initiates **game-process** with random pair of players.
- **game-process**: accepts initial game state, processes actions from players and sends updates. State is updated locally inside `go-loop`

### Multimethods

Multimethods are awesome.
We need to handle lot of cards in a similar way, `cond` would be a total mess.
Instead we just add multimethod handles specific card.

``` clojure
(defmethod apply-card
  :mutator-right-gap
  [game-map player-key card & args]
  ((apply-to-funstruct game-map player-key)
   (fn [funstruct]
     (conj funstruct (gap)))))
```

### Adhoc Hierarchies

Lot of cards are handled the same way.
For example terminal cards are separated token cards with different keys,
but have the same implementation. Adhoc hierarchies handle that very well.

``` clojure
;; build hierarchy
(def terminal-hierarchy
  (-> (make-hierarchy)
      (derive :terminal-left-paren :terminal)
      (derive :terminal-right-paren :terminal)
      (derive :terminal-left-square :terminal)
      (derive :terminal-right-square :terminal)))

;; add hierarchy to multimethod
(defmulti apply-card
  "Modify game-state map and return this map"
  (fn [m p card & args] card)
   :hierarchy #'terminal-hierarchy)

;; add parent implementation
(defmethod apply-card
  :terminal
  [game-map player-key card & args]
  (let [[pos & _] args]
    ((apply-to-funstruct game-map player-key)
     (fn [funstruct]
       (assoc funstruct pos
              {:terminal
               (get-in c/cards [card :value])})))))
```

# Bad Parts

### Confusion

Our bad we didn't provide instructions for people how to play,
we just assumed everyone knows.

First time user press "Play", white rectangle is rolling and seems that nothing works.
Actually, the game is multiplayer and it seeks for an opponent. Our game is not popular yet,
so ask your friend to play with you or open game in two different browser tabs.

### (Clojure/Java)Script

In any way we don't want to say here that ClojureScript is bad or something like that. We didn't actually had anyone in our team who had prior experience with ClojureScript. And it didn't feel like you can start hacking ClojureScript from the first day even if you have experience with JavaScript.

Front-end in ClojureScript was the highest risk in our application and we couldn't manage to implement everything in it in time. So we had to fallback to JavaScript and React.js in order to deliver a playable application. That said, learning curve with ClojureScript is steep and we should have started learning it long before the hackathon.

### Try Catch

Suddenly, client sends a corrupted data to a server, using card that needs target position, without position. To save time we use straightforward approach: wrapped call to `use-card` in `try-catch` block, logged the wrong parameters and returned game map without modifications.

``` clojure
(catch Exception e
  (do
    (u/log
      (.getMessage e)
      "\n"
      "Critical Error:\n\n"
      "Gamestate: " game-map "\n"
      "Player:"     player-key "\n"
      "Card"        (get-card game-map player-key card-pos) "\n"
      "Args[]:"     args "\n")
      game-map))
```

From the client perspective it looks like the actions are not applied and he did something wrong, so he'll try again. But, we are pretty sure there is more elegant solution. 

### Losing the type

Pay attention to the datastructures and operations you apply on them. Consider function which adds card to the tail of vector

``` clojure
(defn add-card [cards card]
  (conj cards card))
```

This function remove all the gaps from the vector

``` clojure
(defn remove-gaps [cards]
  (remove #(= :gap %) cards))
```

But! `remove` transformed `cards` vector to a **sequence**, and next call to `add-card` adds it to the head of the list. So instead `remove` use `filterv`

``` clojure
(defn remove-gaps [cards]
  (filterv #(not= :gap %) cards))
```

### Multimethod Unmap

This is annoying one, we wasted some time to understand why we getting some strange error. The problem is if you change number of arguments of your multimethod, it is **NOT** updated in namespace. Do `ns-unmap` before.

### Private methods

Creating function with `defn` makes hard to refactor namespace and move some functions to another. You can't be sure nobody at external namespace references this function.

Create all functions with `defn-` by default and expose it only if needed.

### core.async error handling

It was really hard to figure out where exactly some exception occured inside `go` block.
Stacktrace was not printed, you had only exception name and guesses what happened.

Appears that `chan` function can accept optional exception handler which is executed
when something bad happens during execution of `put` or `take` callbacks.
So  we were creating channels as follows:

``` clojure
(chan nil nil #(.printStackTrace %))
```

Also make sure that you don't return anything in exception handler function, because all non-nil return values will be placed on channel.

# Future

We plan to continue working on the game in free time and
our first priority improvements are:

- Nice tutorial to help people understand the game
- Rewrite UI from JavaScript/React.js to ClojureScript/Reagent
- Add timeout for the game turn
- Private battles
- Play against computer
- More cards, good art & game balance

# Conclusion

We really enjoyed coding for ClojureCup this year and will definetely participate next year. ClojureCup and hackathons like that is a great way to apply knowledge that you accumulate throughout the year by doing something cool and learning from other teams.

We are a bit saddened that we couldn't deliver everything that we wanted to accomplish in our application but it was educational and fun! Huge thanks to ClojureCup organizers for all the work that was done to make it happen! Also, kudos to all the teams who participated, you're all super creative and we learned a lot from you! See you next year at ClojureCup 2015!

# Final Notes

If you interested in code check out [repo](https://github.com/clojurecup2014/funstructor) 

And finally, if you liked our app, please, vote!

[<img src="http://i.imgur.com/DQxGKT7.jpg">](https://clojurecup.com/#/apps/funstructor)
