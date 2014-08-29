---
layout: post
title: "Clojure Design Patterns"
date: 2014-05-26 00:09
comments: true
categories: [clojure, programming, java, story, patterns]
published: true
---

*Quick* overview of the classic [Design Patterns](http://en.wikipedia.org/wiki/Design_Patterns) in Clojure

<!-- more -->

**Disclaimer:** *Most patterns are easy to implement because
we use dynamic typing, functional programming and, of course,
Clojure. Some of them look wrong and ugly. It's okay.
All characters are fake, coincidences are accidental.*

> Our programming language is fucked up.  
> That's why we need design patterns.
>
> -- Anonymous

### Index

- [Intro](#intro)
- [Episode  1. Command](#command)
- [Episode  2. Strategy](#strategy)
- [Episode  3. State](#state)
- [Episode  4. Visitor](#visitor)
- [Episode  5. Template Method](#template_method)
- [Episode  6. Iterator](#template_method)
- [Episode  7. Memento](#memento)
- [Episode  8. Prototype](#prototype)
- [Episode  9. Mediator](#mediator)
- [Episode 10. Observer](#observer)
- [Episode 11. Interpreter](#interpreter) [RAW]
- [Episode 12. Flyweight](#flyweight)
- [Episode 13. Builder](#builder)

- Chain of responsibility
- Facade [!]
- Singleton
- Abstract Factory
- Factory Method
- Adapter
- Bridge
- Composite
- Decorator
- Proxy
- Cast

### <div id="intro"/>Intro

Two modest programmers **Pedro Veel** and **Eve Dopler**
solving software engineering problems and
applying design patterns.

### <div id="command"/>Episode 1. Command

> Leading IT service provider **"Serpent Hill & R.E.E"**
> acquired new project for USA customer.
> First delivery is a register, login and logout functionality.

*Pedro:* Oh, that's easy. You just need a Command interface...  

``` java
interface Command {
  void execute();
}
```

*Pedro:* Every action should implement it and define `execute` behaviour.

``` java
public class LoginCommand implements Command {

  private String user;
  private String password;

  public LoginCommand(String user, String password) {
    this.user = user;
    this.password = password;
  }

  @Override
  public void execute() {
    DB.login(user, password);
  }
}
```

``` java
public class LogoutCommand implements Command {

  private String user;

  public LogoutCommand(String user) {
    this.user = user;
  }

  @Override
  public void execute() {
    DB.logout(user);
  }
}
```

*Pedro:* Usage is simple as well.

``` java
(new LoginCommand("django", "unCh@1ned")).execute();
(new LogoutCommand("django")).execute();
```

*Pedro:* What do you think, Eve?  
*Eve:* Why are you using redundant wrapping and just don't call `DB.login`?  
*Pedro:* It's important to wrap here, because now we can preserve `Command` objects.  
*Eve:* For what purpose?  
*Pedro:* Delayed call, logging, history tracking, caching, plenty of usages.  
*Eve:* Ok, how about that?  

``` clojure
(defn execute [command]
  (command))

(execute #(db/login "django" "unCh@1ned"))
(execute #(db/logout "django"))
```

*Pedro:* What the hell this hash sign?  
*Eve:* A shortcut for javaish  

``` java
new SomeInterfaceWithOneMethod() {
  @Override
  public void execute() {
    // do
  }
};
```

*Pedro:* Just like `Command` interface...  
*Eve:* Or if you want - no-hash solution.  

``` clojure
(defn execute [command & args]
  (apply command args))

(execute db/login "django" "unCh@1ned")
```

*Pedro:* And how do you save function for delayed call in that case?  
*Eve:* Answer yourself. What do you need to call a function?  
*Pedro:* Its name...  
*Eve:* And?  
*Pedro:* ...arguments.  
*Eve:* Bingo. All you do is saving a pair (*function-name*, *arguments*) and call it whenever you want using
`(apply function-name arguments)`  
*Pedro:* Hmm... Looks simple.  
*Eve:* Definitely, **Command is just a function.**  

### <div id="strategy"/>Episode 2. Strategy

> **Sven Tori** pays a lot of money
> to see a page with list of users.
> But users must be sorted by name and
> users with subscription must appear *before*
> all other users.
> Obviously, because they pay.
> Reverse sorting should keep subscripted users on top.

*Pedro:* Ha, just call `Collections.sort(users, comparator)`
with custom comparator.  
*Eve:* How would you implement it?  
*Pedro:* You need to take `Comparator` interface
and provide implementation for `compare(Object o1, Object o2)` method.
Also you need another implementation for `ReverseComparator`  
*Eve:* Stop talking, show me the code!  

``` java
class SubsComparator implements Comparator<User> {

  @Override
  public int compare(User u1, User u2) {
    if (u1.isSubscription() == u2.isSubscription()) {
      return u1.getName().compareTo(u2.getName());
    } else if (u1.isSubscription()) {
      return -1;
    } else {
      return 1;
    }
  }
}

class ReverseSubsComparator implements Comparator<User> {

  @Override
  public int compare(User u1, User u2) {
    if (u1.isSubscription() == u2.isSubscription()) {
      return u2.getName().compareTo(u1.getName());
    } else if (u1.isSubscription()) {
      return -1;
    } else {
      return 1;
    }
  }
}

// forward sort
Collections.sort(users, new SubsComparator());

// reverse sort
Collections.sort(users, new ReverseSubsComparator());
```

*Pedro:* Could you do the same?  
*Eve:* Yeah, something like that  

``` clojure
(sort (comparator 
       (fn [u1 u2]
         (cond
          (= (:subscription u1)
             (:subscription u2)) (neg? (compare (:name u1)
                                                (:name u2)))
             (:subscription u1) true
             :else false))) users)
```

*Pedro:* Pretty similar.  
*Eve:* But we can do it better  

``` clojure
;; forward sort
(sort-by (juxt (complement :subscription) :name) users)

;; reverse sort
(sort-by (juxt :subscription :name) #(compare %2 %1) users)
```

*Pedro:* Oh my gut! Monstrous oneliners.  
*Eve:* Functions, you know.  
*Pedro:* Whatever, it's very hard to understand what's happening there.

*Eve explains juxt, complement and sort-by*  
*10 minutes later*  

*Pedro:* Very doubtful approach to pass strategy.  
*Eve:* I don't care, **Strategy is just a function passed to another function.**  

### <div id="state"/>Episode 3. State

> Sales person **Karmen Git**
> investigated the market and decided
> to provide user-specific functionality.

*Pedro:* Smooth requirements.  
*Eve:* Let's clarify them.  

- *If user has subscription show him all news in a feed*
- *Otherwise, show him recent 10 news*
- *If he pays money, add them to his account balance*
- *If user doesn't have subscription and there is enough money
to buy subscription, change his state to...*

*Pedro:* State! Awesome pattern. First we make a user state enum  

``` java
public enum UserState {
  SUBSCRIPTION(Integer.MAX_VALUE),
  NO_SUBSCRIPTION(10);

  private int newsLimit;

  UserState(int newsLimit) {
    this.newsLimit = newsLimit;
  }

  public int getNewsLimit() {
    return newsLimit;
  }
}
```

*Pedro:* User logic is following  

``` java
public class User {
  private int money = 0;
  private UserState state = UserState.NO_SUBSCRIPTION;
  private final static int SUBSCRIPTION_COST = 30;

  public List<News> newsFeed() {
    return DB.getNews(state.getNewsLimit());
  }

  public void pay(int money) {
    this.money += money;
    if (state == UserState.NO_SUBSCRIPTION
	    && this.money >= SUBSCRIPTION_COST) {
      // buy subscription
      state = UserState.SUBSCRIPTION;
      this.money -= SUBSCRIPTION_COST;
    }
  }
}
```

*Pedro:* Lets call it

``` java
User user = new User(); // create default user
user.newsFeed(); // show him top 10 news
user.pay(10); // balance changed, not enough for subs
user.newsFeed(); // still top 10
user.pay(25); // balance enough to apply subscription
user.newsFeed(); // show him all news
```

*Eve:* You just hide value that affects  
behaviour inside `User` object. We could use strategy to pass it directly `user.newsFeed(subscriptionType)`.  
*Pedro:* Agreed, State is very close to the Strategy.  
They even have the same UML diagrams. but we encapsulate balance and bind it to user.
*Eve:* I think it achieves the same goal using another mechanism,
from clojure perspective it can be implemented
the same way as strategy pattern. **It is just a first-class function**.  
*Pedro:* But successive calls can change object's state.  
*Eve:* Correct, but it has nothing to do with `Strategy` it is just implementation detail.  
*Pedro:* What about "another mechanism"?  
*Eve:* Multimethods.  
*Pedro:* Multi *what*?  
*Eve:* Look at this  

``` clojure
(defmulti news-feed :user-state)

(defmethod news-feed :subscription [user]
  (db/news-feed))

(defmethod news-feed :no-subscription [user]
  (take 10 (db/news-feed)))
```

*Eve:* And `pay` function it's just a plain function, which changes state of object. We don't like state too much in clojure, but if you wish.  

``` clojure
(def user (atom {:name "Jackie Brown"
                 :balance 0
                 :user-state :no-subscription}))
				 
(def ^:const SUBSCRIPTION_COST 30)

(defn pay [user amount]
  (swap! user update-in [:balance] + amount)
  (when (and (>= (:balance @user) SUBSCRIPTION_COST)
             (= :no-subscription (:user-state @user)))
    (swap! user assoc :user-state :subscription)
    (swap! user update-in [:balance] - SUBSCRIPTION_COST)))

(news-feed @user) ;; top 10
(pay user 10)
(news-feed @user) ;; top 10
(pay user 25)
(news-feed @user) ;; all news
```

*Pedro:* Is dispatching by multimethods better than dispatching by enum?  
*Eve:* No, in this particlular case, but in general yes.  
*Pedro:* Explain, please  
*Eve:* Do you know what *double dispatch* is?  
*Pedro:* Not sure.  
*Eve:* Well, it is topic for `Visitor` pattern.

### <div id="visitor"/>Episode 4. Visitor

> **Natanius S. Selbys** suggested to implement functionality
> allows users export their messages, activities and achievements
> in different formats.

*Eve:* So, how do you plan to do it?  
*Pedro:* We have one hierarchy for item types
(Message, Activity) and another
for file formats (PDF, XML)  

``` java
abstract class Format { }
class PDF extends Format { }
class XML extends Format { }

public abstract class Item {
  void export(Format f) {
    throw new UnknownFormatException(f);
  }
  abstract void export(PDF pdf);
  abstract void export(XML xml);
}

class Message extends Item {
  @Override
  void export(PDF f) {
    PDFExporter.export(this);
  }

  @Override
  void export(XML xml) {
    XMLExporter.export(this);
  }
}

class Activity extends Item {
  @Override
  void export(PDF pdf) {
    PDFExporter.export(this);
  }

  @Override
  void export(XML xml) {
    XMLExporter.export(this);
  }
}
```

*Pedro:* That's all.  
*Eve:* Nice, but how do you dispatch on argument type?  
*Pedro:* What the problem?  
*Eve:* Consider this snippet  

``` java
Item i = new Activity();
Format f = new PDF();
i.export(f);
```

*Pedro:* Nothing suspicious here.  
*Eve:* Actually, if you run this code you get `UnknownFormatException`  
*Pedro:* Wait...Really?  
*Eve:* In java you can use only *single dispatch*. That means if you call `i.export(f)`
you dispatches on the actual type of `i`, not `f`.  
*Pedro:* I'm surprised. So, there is no dispatch on argument type?  
*Eve:* That's what visitor hack for. After you got a dispatch on `i` type, you
additionally call `f.someMethod(i)` and dispatched on `f` type.  
*Pedro:* How that looks in code?  
*Eve:* You separately define export operations for all types as a `Visitor`  

``` java
public interface Visitor {
  void visit(Activity a);
  void visit(Message m);
}

public class PDFVisitor implements Visitor {
  @Override
  public void visit(Activity a) {
    PDFExporter.export(a);
  }

  @Override
  public void visit(Message m) {
    PDFExporter.export(m);
  }
}
```

*Eve:* Your items change signature to accept different visitors.  

``` java
public abstract class Item {
  abstract void accept(Visitor v);
}

class Message extends Item {
  @Override
  void accept(Visitor v) {
    v.visit(this);
  }
}

class Activity extends Item {
  @Override
  void accept(Visitor v) {
    v.visit(this);
  }
}
```

*Eve:* To use it you may call  

``` java
Item i = new Message();
Visitor v = new PDFVisitor(); 
i.accept(v);
```

*Eve:* And everything works fine.
Moreover, you can add new operations for
activities and messages
by just defining new visitors and
without changing their code.  
*Pedro:* That's really useful. But implementation is tough,
it is the same for clojure?  
*Eve:* Not really, clojure supports it natively via multimethods  
*Pedro:* Multi *what*?  
*Eve:* Just follow the code...
First we define dispatcher *function*

``` clojure
(defmulti export
  (fn [item format] [(:type item) format]))
```

*Eve:* It accepts `item` and `format` to be exported. Examples:

``` clojure
;; Message
{:type :message :content "Say what again!"}
;; Activity
{:type :activity :content "Quoting Ezekiel 25:17"}
;; Formats
:pdf, :xml
```

*Eve:* And now you just provide a functions
for different combinatations, and dispatcher decide which one to call.

``` clojure
(defmethod export [:activity :pdf] [item format]
  (exporter/activity->pdf item))

(defmethod export [:activity :xml] [item format]
  (exporter/activity->xml item))

(defmethod export [:message :pdf] [item format]
  (exporter/message->pdf item))

(defmethod export [:message :xml] [item format]
  (exporter/message->xml item))
```

*Pedro:* What if unknown format passed?  
*Eve:* We could specify default dipatcher function.  

``` clojure
(defmethod export :default [item format]
  (throw (IllegalArgumentException. "not supported")))

```

*Pedro:* Ok, but there is no hierarchy for `:pdf` and `:xml`.
They are just keywords?  
*Eve:* Correct, simple problem - simple solution.
If you need advanced features, you could use *adhoc hierarchies*
or dispatch by `class`.  

``` clojure
(derive ::pdf ::format)
(derive ::xml ::format)
```

*Pedro:* Quadrocolons?!  
*Eve:* Assume they are just keywords.  
*Pedro:* Ok.  
*Eve:* Then you add functions for every dispatch type
`::pdf`, `::xml` and `::format`  

``` clojure
(defmethod export [:activity ::pdf])
(defmethod export [:activity ::xml])
(defmethod export [:activity ::format])
```

*Eve:* If some new format (i.e. csv) appears in the system  

``` clojure
(derive ::csv ::format)
```

*Eve:* It will be dispatched to `::format` function,
until you add a separate `::csv` function.  
*Pedro:* Seems good.  
*Eve:* Definitely, much easier.  
*Pedro:* So, basically, **if a language support multiple dispatch,
you don't need Visitor pattern**?  
*Eve:* Exactly.  

### <div id="template_method"/>Episode 5. Template Method

> MMORPG **Mech Dominore Fight Saga** requested a game bot
> for their VIP users. Not fair.

*Pedro:* First, we must decide what actions 
should be automated with bot.  
*Eve:* Have you ever played RPG?  
*Pedro:* Forntunately, no  
*Eve:* Oh my... Let's go, I'll show you...  

*2 weeks later*

*Pedro:* ...fantastic, I found epic sword, which has +100 attack.  
*Eve:* Unbelievable. But now, it's time for bot.  
*Pedro:* Easy-peasy. We could select following events  

- Battle
- Quest
- Opening Chest

*Pedro:* Characters behave differently in
different events, for example mages cast spells in battle,
but rogues prefer silent melee combat, locked chests are skipped
by most characters, but rogues can unlock them, etc.  
*Eve:* Looks like ideal candidate for `Template Method`?  
*Pedro:* Yes. We define abstract algorithm, and then specify
differences in subclasses.  

``` java
public abstract class Character {
  void moveTo(Location loc) {
    if (loc.isQuestAvailable()) {
      Journal.addQuest(loc.getQuest());
    } else if (loc.containsChest()) {
      handleChest(loc.getChest());
    } else if (loc.hasEnemies()) {
      attack(loc.getEnemies());
    }
    moveTo(loc.getNextLocation());
  }

  private void handleChest(Chest chest) {
    if (!chest.isLocked()) {
      chest.open();
    } else {
      handleLockedChest(chest);
    }
  }

  abstract void handleLockedChest(Chest chest);
  abstract void attack(List<Enemy> enemies);
}
```

*Pedro:* We've separated to `Character` class everything common to all characters.
Now we can create subclasses, that define how character should behave in specific situation.
In out case: *handling locked chests* and *attacking enemies*.  
*Eve:* Let's start with a Mage class.  
*Pedro:* Mage? Okay.
He can't open locked chest, so implementation is just *do nothing*.
And if he is attacking enemies, if there are more than 10 enemies,
freeze them, and cast teleport to run away. If there are 10 enemies or less
cast fireball on each of them.  

``` java
public class MageCharacter extends Character {
  @Override
  void handleLockedChest(Chest chest) {
    // do nothing
  }

  @Override
  void attack(List<Enemy> enemies) {
    if (enemies.size() > 10) {
      castSpell("Freeze Nova");
      castSpell("Teleport");
    } else {
      for (Enemy e : enemies) {
        castSpell("Fireball", e);
      }
    }
  }
}
```

*Eve:* Excellent, what about Rogue class?  
*Pedro:* Easy as well, rogues can unlock chests and
prefer silent combat, handle enemies one by one.  

``` java
public class RogueCharacter extends Character {
  @Override
  void handleLockedChest(Chest chest) {
    chest.unlock();
  }

  @Override
  void attack(List<Enemy> enemies) {
    for (Enemy e : enemies) {
      invisibility();
	  attack("backstab", e);
    }
  }
}
```

*Eve:* Excellent. But how this approach is differrent from Strategy?  
*Pedro:* What?  
*Eve:* I mean, you redefined behaviour by using subclasses,
but in Strategy pattern you did the same:
redefined behaviour by using functions.  
*Pedro:* Well, another approach.  
*Eve:* State was handled with another approach as well.  
*Pedro:* What are you trying to say?  
*Eve:* You are solving the same kind of problem,
but change the approach to it.  
*Pedro:* How do yo solve this problem using strategy in clojure?  
*Eve:* Just pass a set of specific functions for each character. For example, your abstract move may look like:  

``` clojure
(defn move-to [character location]
  (cond
   (quest? location)
   (journal/add-quest (:quest location))

   (chest? location)
   (handle-chest (:chest location))

   (enemies? location)
   (attack (:enemies location)))
  (move-to character (:next-location location)))
```

*Eve:* To add character-specific implementation
of methods `handle-chest` and `attack`, implement them
and pass as an argument.  

``` clojure
;; Mage-specific actions
(defn mage-handle-chest [chest])

(defn mage-attack [enemies]
  (if (> (count enemies) 10)
    (do (cast-spell "Freeze Nova")
        (cast-spell "Teleport"))
    ;; otherwise
    (doseq [e enemies]
      (cast-spell "Fireball" e))))

;; Signature of move-to will change to

(defn move-to [character location
               & {:keys [handle-chest attack]
                  :or {handle-chest (fn [chest])
                       attack (fn [enemies]
                                (run-away))}}]
;; previous implementation
)
```

*Pedro:* OMG, what's happening there?  
*Eve:* We changed signature of `move-to` to accept
`handle-chest` and `attack` functions.  

``` clojure
(move-to character location
  :handle-chest mage-handle-chest
  :attack       mage-attack)
```

*Eve:* Keep in mind that if these functions are not provided
we use default behavior: do nothing for `handle-chest` and
run away from enemies in `attack`  
*Pedro:* Fine, but is this better than approach by subclassing? Seems that we have a lot of redundant information in
`move-to` call.  
*Eve:* It's fixable, just define this call once, and give it
alias  

``` clojure
(defn mage-move [character location]
  (move-to character location
    :handle-chest mage-handle-chest
    :attack       mage-attack))
```

*Eve:* Or use multimethods, it's even better.  

``` clojure
(defmulti move 
  (fn [character location] (:class character)))

(defmethod move :mage [character location]
  (move-to character location
    :handle-chest mage-handle-chest
    :attack       mage-attack))
```

*Pedro:* I understand. But why do you think pass as argument is better than subclassing?  
*Eve:* You can change behaviour dynamically. Assume your mage
has no mana, so instead of trying to cast fireballs, he can just teleport and run away, you just provide new function.  
*Pedro:* Makes sense. **Functions everywhere**.  

### <div id="iterator"/>Episode 6. Iterator

> Technical consultant **Kent Podiololis**
> complains for C-style loops usage.
>
> "Are we in 1980 or what?"

*Pedro:* We definitely should use pattern Iterator from java.  
*Eve:* Don't be fool, nobody's using `java.util.Iterator`  
*Pedro:* Everybody use it implicitly in `for-each` loop. It's a good way to traverse a container.  
*Eve:* What does it mean *"to traverse a container"*?  
*Pedro:* Formally, the container should provide two methods for you:  
`next()` to return next element and `hasNext()` to return true if container has more elements.  
*Eve:* Ok. Do you know what linked list is?  
*Pedro:* Singly linked list?  
*Eve:* Singly linked list.  
*Pedro:* Sure. It is a container consists of nodes. 
Each node has a data value and reference to the next node.
And `null` value if there is no next node.  
*Eve:* Correct. Now tell me how traversing such list is differ
from traversing via iterator?  
*Pedro:* Emmm...  

Pedro wrote two traversing snippets:

* Traversing using iterator

``` java
Iterator i;
while (i.hasNext()) {
  i.next();
}
```

* Traversing using linked list

``` java
Node next = root;
while (next != null) {
  next = next.next;
}
```

*Pedro:* They are pretty similar...What is analogue of `Iterator` in clojure?  
*Eve:* `seq` function.  


``` clojure
(seq [1 2 3])       => (1 2 3)
(seq (list 4 5 6))  => (4 5 6)
(seq #{7 8 9})      => (7 8 9)
(seq (int-array 3)) => (0 0 0)
(seq "abc")         => (\a \b \c)
```

*Pedro:* It returns a list...  
*Eve:* Because **Iterator is just a list**  
*Pedro:* But is it possible to make `seq` works on custom datastructures?  
*Eve:* Implement `clojure.lang.Seqable` interface  

``` clojure
(deftype RedGreenBlackTree [& elems]
  clojure.lang.Seqable
  (seq [self]
	;; traverse element in needed order
	))
```

*Pedro:* Fine then.

### Episode 7: Memento

> User **Chad Bogue** lost the message he was writing
> for a two days. Implement save button for him.

*Pedro:* I don't believe there are people who
can type in textbox for a two days. Two. Days.  
*Eve:* Let's *save* him.  
*Pedro:* I *googled* this problem. Most popular approach
to implement save button is Memento pattern.
You need *originator*, *caretaker* and *memento* objects.  
*Eve:* What's that?  
*Pedro:* *Originator* is just an object or state that we want to preserve.
(text inside a textbox), *caretaker* is responsible to save state (save button)
and *memento* is just an object to encapsulate state.  

``` java
public class TextBox {
  // state for memento
  private String text = "";

  // state not handled by memento
  private int width = 100;
  private Color textColor = Color.BLACK;

  public void type(String s) {
    text += s;
  }

  public Memento save() {
    return new Memento(text);
  }

  public void restore(Memento m) {
    this.text = m.getText();
  }

  @Override
  public String toString() {
    return "[" + text + "]";
  }
}
```

*Pedro:* Memento is just immutable object  

``` java
public final class Memento {
  private final String text;

  public Memento(String text) {
    this.text = text;
  }

  public String getText() {
    return text;
  }
}
```

*Pedro:* And caretaker is a demo  

``` java
// open browser, init empty textbox
TextBox textbox = new TextBox();

// type something into it
textbox.type("Dear, Madonna\n");
textbox.type("Let me tell you what ");

// press button save
Memento checkpoint1 = textbox.save();

// type again
textbox.type("song 'Like A Virgin' is about. ");
textbox.type("It's all about a girl...");

// suddenly browser crashed, restart it, reinit textbox
textbox = new TextBox();

// but it's empty! All work is gone!
// not really, you rollback to last checkpoint
textbox.restore(checkpoint1);
```

*Pedro:* Just a note if you want a multiple checkpoints, save memento's to the list.  
*Eve:* Looks as a bunch of nouns, but actually it's all about two functions `save` and `restore`.  

``` clojure
(def textbox (atom {}))

(defn init-textbox [] 
 (reset! textbox {:text ""
                  :color :BLACK
                  :width 100}))

(def memento (atom nil))

(defn type-text [text]
  (swap! textbox
    (fn [m]
      (update-in m [:text] (fn [s] (str s text))))))

(defn save []
  (reset! memento (:text @textbox)))

(defn restore []
  (swap! textbox assoc :text @memento))
```

*Eve:* And demo as well.

``` clojure
(init-textbox)
(type-text "'Like A Virgin' ")
(type-text "it's not about this sensitive girl ")
(save)
(type-text "who meets nice fella")
;; crash
(init-textbox)
(restore)
```

*Pedro:* It's pretty the same code.  
*Eve:* Yes, but you must care about memento immutability  
*Pedro:* What does it mean?  
*Eve:* You are lucky, that you got `String` object in this example,
`String` is immutable. But if you have something, that
may change its internal state, you need to perform deep copy of this object for memento.  
*Pedro:* Oh, right. It's just a recursive `clone()` calls to
obtain prototype.  
*Eve:* We will talk about Prototype in a minute, but just
remember that `Memento` is not about *caretaker* and *originator*, it is about **save and restore**.  

### <div id="prototype"/>Episode 8: Prototype

> **Dex Ringeus** detected that users feel uncomfortable
> with registration form. Make it more usable.

*Pedro:* So, what's the problem with the registration?  
*Eve:* There are lot of fields users bored to type in.  
*Pedro:* For example?  
*Eve:* For example, `weight`. Having such field scares
90% of female users.  
*Pedro:* But this field is important for our analytics
system, we make food and clothes recomendations based on that field.  
*Eve:* Then, make it optional, and if it is not provided, take
some default value.  
*Pedro:* `60 kg` good?  
*Eve:* I think so.  
*Pedro:* Ok, give me two minutes.  

*2 hours later*

*Pedro:* I suggest to use some registration *prototype*
which has all fields are filled with default values. 
After user completes the form we modify filled values.  
*Eve:* Sounds great.  
*Pedro:* Here it is our standard registration form, with prototype in `clone()` method.  

``` java
public class RegistrationForm implements Cloneable {
  private String name = "Zed";
  private String email = "zzzed@gmail.com";
  private Date dateOfBirth = new Date(1970, 1, 1);
  private int weight = 60;
  private Gender gender = Gender.MALE;
  private Status status = Status.SINGLE;
  private List<Child> children = Arrays.asList(new Child(Gender.FEMALE));
  private double monthSalary = 1000;
  private List<Brand> favouriteBrands = Arrays.asList("Adidas", "GAP");
  // few hundreds more properties

  @Override
  protected RegistrationForm clone() throws CloneNotSupportedException {
    RegistrationForm prototyped = new RegistrationForm();
      prototyped.name = name;
      prototyped.email = email;
      prototyped.dateOfBirth = (Date)dateOfBirth.clone();
      prototyped.weight = weight;
      prototyped.status = status;
      List<Child> childrenCopy = new ArrayList<Child>();
      for (Child c : children) {
        childrenCopy.add(c.clone());
      }
      prototyped.children = childrenCopy;
      prototyped.monthSalary = monthSalary;
      List<String> brandsCopy = new ArrayList<String>();
      for (String s : favouriteBrands) {
        brandsCopy.add(s);
      }
      prototyped.favouriteBrands = brandsCopy;
    return  prototyped;
  }
}
```

*Pedro:* Every time we create a user, call `clone()` and then override needed properties.  
*Eve:* Awful! In mutable world `clone()` is needed to create
new object with the same properties.
The hard part is the copy must be deep, i.e. instead of copying
reference you need recursively `clone()` other objects,
and what if one of them doesn't have `clone()`...  
*Pedro:* That's the problem and this pattern solves it.  
*Eve:* I don't think it is a solution if you need to implement
clone every time you adding new object.  
*Pedro:* How clojure avoid this?  
*Eve:* Clojure has immutable data structures. That's all.  
*Pedro:* How does it solve prototype problem?  
*Eve:* Every time you modify object, you get
a fresh new immutable copy of your data, and old one is not changed. **Prototype is not needed in immutable world**  

``` clojure
(def registration-prototype
	 {:name          "Zed"
	  :email         "zzzed@gmail.com"
	  :date-of-birth "1970-01-01"
	  :weight        60
	  :gender        :male
	  :status        :single
	  :children      [{:gender :female}]
	  :month-salary  1000
	  :brands        ["Adidas" "GAP"]})

;; return new object
(assoc registration-prototype 
	 :name "Mia Vallace"
	 :email "tomato@gmail.com"
	 :weight 52
	 :gender :female
	 :month-salary 0)
```

*Pedro:* Great! But how this affects performance?  
Copying million rows each time you adding new value seems
time consuming operation.  
*Eve:* No, it is not. Go to Google and search for *persistent data structures* and *structural sharing*  
*Pedro:* Thanks a lot.  

### <div id="mediator"/>Episode 9: Mediator

> Recently performed external code review
> shows a lot of issues with current codebase.
> **Veerco Wierde** emphasizes tight coupling 
> in chat application.

*Eve:* What is tight coupling?  
*Pedro:* It's the problem when objects
know too much about each other.  
*Eve:* Could you be more specific?  
*Pedro:* Look at the current chat implementation  

``` java
public class User {
  private String name;
  List<User> users = new ArrayList<User>();

  public User(String name) {
    this.name = name;
  }

  public void addUser(User u) {
    users.add(u);
  }

  void sendMessage(String message) {
    String text = String.format("%s: %s\n", name, message);
    for (User u : users) {
      u.receive(text);
    }
  }

  private void receive(String message) {
    // process message
  }
}
```

*Pedro:* The problem here is the user knows
everything about other users. It is very hard to use and maintain such code.
When new user connects to the chat, you must add a reference to him via `addUser`
for every existing user.  
*Eve:* So, we just move one piece of responsibility to another class?  
*Pedro:* Yes, kind of. We create *mega-aware* class, called mediator, that binds
all parts together. Obviously, each part knows only about mediator.  

``` java
public class User {
  String name;
  private Mediator m;

  public User(String name, Mediator m) {
    this.name = name;
    this.m = m;
  }

  public void sendMessage(String text) {
    m.sendMessage(this, text);
  }

  public void receive(String text) {
    // process message
  }
}

public class Mediator {

  List<User> users = new ArrayList<User>();

  public void addUser(User u) {
    users.add(u);
  }

  public void sendMessage(User u, String text) {
    for (User user : users) {
      u.receive(text);
    }
  }
}
```

*Eve:* That was easy.  
*Pedro:* Profit may be underestimated, but
if you have hundreds of mutually connected components
(UI for example) mediator is really a savior.  
*Eve:* Agreed.  
*Pedro:* Now the clojure turn.  
*Eve:* Ok...let's look...your mediator is responsible
for *saving users* and *sending messages*  

``` clojure
(def mediator
  (atom {:users []
         :send (fn [users text]
			     (map #(receive % text) users))}))

(defn add-user [u]
	(swap! mediator 
	  (fn [m]
	    (update-in m [:users] conj u))))

(defn send-message [u text]
	(let [send-fn (:send @mediator)
	      users (:users @mediator)]
	  (send-fn users (format "%s: %s\n" (:name u) text))))

(add-user {:name "Mister White"})
(add-user {:name "Mister Pink"})
(send-message {:name "Joe"} "Toby?")
```

*Pedro:* Good enough.

### <div id="observer"/>Episode 10: Observer

> Independent security commision
> detected hacker **Dartee Hebl** has over a billion
> dollars balance on his account.
> Track big transactions to the accounts.

*Pedro:* Are we *Holms*es?  
*Eve:* No, but there is a lack of logging in the system,
need to find a way to track all changes to balance.  
*Pedro:* We could add observers. Every time we modify
balance, if change is *big enough*, notify about this and track the reason. We need `Observer` interface  

``` java
public interface Observer {
  void notify(User u);
}
```

*Pedro:* And two specific observers  

``` java
class MailObserver implements Observer {
  @Override
  public void notify(User user) {
    MailService.sendToFBI(user);
  }
}

class BlockObserver implements Observer {
  @Override
  public void notify(User u) {
    DB.blockUser(u);
  }
}
```

*Pedro:* `Tracker` class would be responsible
for managing observers.  

``` java
public class Tracker {
  private Set<Observer> observers = new HashSet<Observer>();

  public void add(Observer o) {
    observers.add(o);
  }

  public void update(User u) {
    for (Observer o : observers) {
      o.notify(u);
    }
  }
}
```

*Pedro:* And the last part: init tracker with the user and
modify its `addMoney` method. If transcation amount is greaterthan `100$`, notify FBI and block this user.  

``` java
public class User {
  String name;
  double balance;
  Tracker tracker;

  public User() {
    initTracker();
  }

  private void initTracker() {
    tracker = new Tracker();
    tracker.add(new MailObserver());
    tracker.add(new BlockObserver());
  }


  public void addMoney(double amount) {
    balance += amount;
    if (amount > 100) {
      tracker.update(this);
    }
  }
}
```

*Eve:* Why are you created two separate observers?
You could use it in a one.  

``` java
class MailAndBlock implements Observer {
  @Override
  public void notify(User u) {
    MailService.sendToFBI(u);
    DB.blockUser(u);
  }
}
```

*Pedro:* Single responsibility principle.  
*Eve:* Oh, yeah.  
*Pedro:* And you can compose observer functionality
dynamically.  
*Eve:* I see your point.  

``` clojure
;; Tracker

(def observers (atom #{}))

(defn add [observer]
  (swap! observers conj observer))

(defn notify [user]
  (map #(apply % user) @observers))

;; Fill Observers

(add (fn [u] (mail-service/send-to-fbi u)))
(add (fn [u] (db/block-user u)))

;; User

(defn add-money [user amount]
  (swap! user
    (fn [m]
      (update-in m [:balance] + amount)))
  ;; tracking
  (if (> amount 100) (notify)))
```

*Pedro:* It's a pretty the same way?  
*Eve:* Yeah, but there is one improvement using watches.

``` clojure
(add-watch
  user
  :money-tracker 
  (fn [k r os ns] 
    (if (< 100 (- (:balance ns) (:balance os)))
      (notify))))
```

*Pedro:* Why is that better?
*Eve:* First of all, our `add-money` function is clean, it just adds money. Also, watcher tracks *every* change to the state, not the ones we handle in mutator functions, like `add-money`
*Pedro:* That's awesome!

### <div id="interpreter"/>Episode 11: Interpreter

> **Bertie Prayc** stole important data from our server
> and shared it via BitTorrent system.
> Create a fake account for *Bertie* to discredit him.

*Pedro:* BitTorrent system based on `.torrent` files.
We need to write [Bencode](http://en.wikipedia.org/wiki/Bencode) encoder.  
*Eve:* Yes, but first let's agree on the format spec  

Bencode encoding rules:

- Integer `N` is encoded as `i<N>e`. (*42 = i42e*)
- String `S` is encoded as `<length>:<contents>` (*hello = 5:hello*)
- List of values is encoded as `l<contents>e` (*[1, "Bye"] = li1e3:Byee*)
- Map of values is encoded as `d<contents>e` (*{"R" 2, "D" 2} = d1:Ri2e1:Di2ee*)

*Pedro:* Seems easy.  
*Eve:* Maybe, but take into account that values may be nested, list inside list, etc.  
*Pedro:* Sure. I think we can use *Interpreter* pattern for bencode encoding.  
*Eve:* Try it.  

``` java
todo
```

``` clojure
todo
```

## TODO

### <div id="flyweight"/>Episode 12: Flyweight

> Administrators of the lawyer firm **Cristopher, Matton & Pharts**
> detected that reporting system consumes a lot of memory
> and garbage collector continiously hangs system for seconds. Fix that.

*Pedro:* I've seen this issue before.  
*Eve:* What's wrong?  
*Pedro:* They have realtime charts with a lot of different points
it's a huge amount of memory. As a result garbage collector stops the system.
*Eve:* Hmmm, what we can do?  
*Pedro:* Not much, caching does not help us because points are...  
*Eve:* Wait!  
*Pedro:* What?  
*Eve:* They use age values for points, why not precompute these points for most common ages?
Say for age [0, 100]__
*Pedro:* You mean use *Flyweight* pattern?  
*Eve:* I mean reuse objects.  

``` java
class Point {
  int x;
  int y;

  /* some other properties*/

  // precompute 10000 point values at class loading time
  private static Point[][] CACHED;
  static {
    CACHED = new Point[100][];
    for (int i = 0; i < 100; i++) {
      CACHED[i] = new Point[100];
      for (int j = 0; j < 100; j++) {
        CACHED[i][j] = new Point(i, j);
      }
    }
  }

  Point(int x, int y) {
    this.x = x;
    this.y = y;
  }

  static Point makePoint(int x, int y) {
    if (x >= 0 && x < 100 &&
        y >= 0 && y < 100) {
      return CACHED[x][y];
    } else {
      return new Point(x, y);
    }
  }
}
```

*Pedro:* For this pattern we need two things: precompute
most used points at a startup time, and use static factory
method instead of constructor to return cached object.  
*Eve:* Have you tested it?  
*Pedro:* Sure, the system works like a clock.  
*Eve:* Excellent, here is my version  

``` clojure
(defn make-point [x y]
  [x y {:some "Important Properties"}])

(def CACHE
  (let [cache-keys (for [i (range 100) j (range 100)] [i j])]
	  (zipmap cache-keys (map #(apply make-point %) cache-keys))))

(defn make-point-cached [x y]
  (let [result (get CACHE [x y])]
    (if result
	  result
	  (make-point x y))))
```

*Eve:* It creates a flat map with pair `[x, y]` as a key, instead of two-dimensional array.  
*Pedro:* Pretty the same.  
*Eve:* No, it is much flexible, you can't use two-dimensional array if you need to cache three points or non-integer values.
*Pedro:* Oh, got it.  
*Eve:* Even better, in clojure you can use `memoize` function
to cache calls to factory function `make-point`  

``` clojure
(def make-point-memoize (memoize make-point))
```

*Eve:* Every call (except first one) with the same parameters
return cached value.  
*Pedro:* That's awesome!  
*Eve:* Of course, but remember if your function has side-effects, memoization is bad idea.  

### <div id="builder"/>Episode 13: Builder

> **Tuck Brass** complains that his old automatic
> coffee-making system is very slow in usage.
> Customers can't wait long enough and going away.

*Pedro:* Need to understand what's the problem exactly?  
*Eve:* I've researched, system is old, written in *COBOL* and built around expert system *question-answer*. They were very popular long time ago.  
*Pedro:* What do you mean by *"question-answer"*  
*Eve:* There is operator in front of terminal. System asks: *"Do yo want to add water?"*, operator answers *"Yes"*. Then system asks again: *"Do you want to add coffee"*, operator answers *"Yes"* and so forth.  
*Pedro:* It's a nightmare, I just want coffee with milk. Why they don't use predefined options: coffee with milk, coffee with sugar and so on.  
*Eve:* Because it is the raisin of the system: customer can make coffee with any set of ingridients *by yourself*  
*Pedro:* Okay, let's fix it with Builder pattern.  

``` java
public class Coffee {
  private String coffeeName; // required
  private double amountOfCoffee; // required
  private double water; // required
  private double milk; // optional
  private double sugar; // optional
  private double cinnamon; // optional

  private Coffee() { }

  public static class Builder {
    private String builderCoffeeName;
    private double builderAmountOfCoffee; // required
    private double builderWater; // required
    private double builderMilk; // optional
    private double builderSugar; // optional
    private double builderCinnamon; // optional

    public Builder() { }

    public Builder setCoffeeName(String name) {
      this.builderCoffeeName = name;
      return this;
    }

    public Builder setCoffee(double coffee) {
      this.builderAmountOfCoffee = coffee;
      return this;
    }

    public Builder setWater(double water) {
      this.builderWater = water;
      return this;
    }

    public Builder setMilk(double milk) {
      this.builderMilk = milk;
      return this;
    }

    public Builder setSugar(double sugar) {
      this.builderSugar = sugar;
      return this;
    }

    public Builder setCinnamon(double cinnamon) {
      this.builderCinnamon = cinnamon;
      return this;
    }

    public Coffee make() {
      Coffee c = new Coffee();
        c.coffeeName = builderCoffeeName;
        c.amountOfCoffee = builderAmountOfCoffee;
        c.water = builderWater;
        c.milk = builderMilk;
        c.sugar = builderSugar;
        c.cinnamon = builderCinnamon;

      // check required parameters and invariants
      if (c.coffeeName == null || c.coffeeName.equals("") ||
          c.amountOfCoffee <= 0 || c.water <= 0) {
        throw new IllegalArgumentException("Provide required parameters");
      }

      return c;
    }
  }
}
```

*Pedro:* As you see, you can't instantiate `Coffee` class easily, you need to set parameters with nested `Builder` class

``` java
Coffee c = new Coffee.Builder()
        .setCoffeeName("Royale Coffee")
        .setCoffee(15)
        .setWater(100)
        .setMilk(10)
        .setCinnamon(3)
        .make();
```

*Pedro:* Calling to method `make` checks all required parameters, and could validate and throw an exception if object is in inconsistent state.  
*Eve:* Awesome functionality, but why so verbose?
*Pedro:* Beat it.
*Eve:* A piece of cake, clojure supports optional parameters

``` clojure
(defn make-coffee [name amount water
                   & {:keys [milk sugar cinnamon]
                      :or {milk 0 sugar 0 cinnamon 0}}]
  ;; definition goes here
  )

(make-coffee "Royale Coffee" 15 100
             :milk 10
             :cinnamon 3)
```

*Pedro:* Aha, you have three required parameters and three optionals, but required parameters still without names.  
*Eve:* What do you mean?  
*Pedro:* From the client call I see number `15` but I have no idea what it might be.  
*Eve:* Agreed. Then, let's make all parameters are named and add precondition for required, the same way you do with the builder.

```clojure
(defn make-coffee
  [& {:keys [name amount water milk sugar cinnamon]
      :or {name "" amount 0 water 0 milk 0 sugar 0 cinnamon 0}}]
  {:pre [(not (empty? name))
         (> amount 0)
         (> water 0)]}
  ;; definition goes here		 
  )

(make-coffee :name "Royale Coffee"
             :amount 15
             :water 100
             :milk 10
             :cinnamon 3)
```

*Eve:* As you see all parameters are named and all required params are checked in `:pre` constraint. If constraints are violated `AssertionError` is thrown.  
*Pedro:* Interesting, `:pre` is a part of a language?  
*Eve:* Sure, it's just a simple assertion. There is also `:post` constraint, with the similar effect.  
*Pedro:* Hm, okay. But as you know `Builder` pattern often used as a mutable datastucture, `StringBuilder` for example.  
*Eve:* It's not a part of clojure philosophy to use mutables, but if you *really* want, no problem.
Just create a new class with `deftype` and do not forget to use `volatile-mutable` on the properties you want to mutate.  
*Pedro:* Where is the code?  
*Eve:* Here is example of custom implementation of mutable `StringBuilder` in clojure. It has a lot of drawbacks and limitations but you've got the idea.

```clojure
;; interface
(defprotocol IStringBuilder
	(append [this s])
	(to-string [this]))

;; implementation
(deftype ClojureStringBuilder [charray ^:volatile-mutable last-pos]
	IStringBuilder
	(append [this s] 
	  (let [cs (char-array s)]
	    (doseq [i (range (count cs))]
	      (aset charray (+ last-pos i) (aget cs i))))
	  (set! last-pos (+ last-pos (count s))))
	(to-string [this] (apply str (take last-pos charray))))

;; clojure binding
(defn new-string-builder []
  (ClojureStringBuilder. (char-array 100) 0))

;; usage
(def sb (new-string-builder))
(append sb "Toby Wong")
(to-string sb) => "Toby Wong"
(append sb " ")
(append sb "Toby Chung") => "Toby Wang Toby Chung"
```

*Pedro:* Not as hard as I thought.

### Episode 14: Facade

> 

### Episode 15: Singleton

### Cast

> A long time ago in a galaxy far, far away...

With the lack of imagination all characters
and names are just anagrams.

**Pedro Veel** - Developer  
**Eve Dopler** - Developer  
**Serpent Hill & R.E.E.** - Enterprise Hell  
**Sven Tori** - Investor  
**Karmen Git** - Marketing  
**Natanius S. Selbys** - Business Analyst  
**Mech Dominore Fight Saga** - Heroes of Might and Magic  
**Kent Podiololis** - I don't like loops  
**Chad Bogue** - Douchebag  
**Dex Ringeus** - UX Designer  
**Veerco Wierde** - Code Review  
**Dartee Hebl** - Heartbleed  
**Bertie Prayc** - Cyber Pirate  
**Cristopher, Matton & Pharts** - Important Charts & Reports  
**Tuck Brass** - Starbucks  
