---
layout: post
title: "Eclipse Speedup"
date: 2012-12-23 18:00
comments: true
sharing: true
categories: [eclipse, java, ide]
published: true
---

Have you been in situation where most of the time in eclipse you were waiting for
some operation to be finished?

<!-- more -->

![](http://upload.wikimedia.org/wikipedia/en/3/34/Eclipse-logo.png)

If you here, probably, the answer is yes. You are bored with that slow functionality and want
to increase eclipse performance to make your development work more comfortable. It is the right thing to do.

*Warning:* Most frequent advice I heard *to add more RAM*. Probably, you can buy better workstation with better CPU.
Add SSD to make filesystem operations faster. We assume you can't afford all that. All you can is setup your
eclipse instance. All preferences based on `Eclipse SDK Version: 4.3.0 Build id: I20121031-2000` for ubuntu, but
almost everything is identical in other versions for other platforms.

### Eclipse tweaks

#### Plugins

First time I found the power of plugins I was excited. I installed them more and more until I couldn't
work comfortable with eclipse. Probably, you have a lot of plugins, just **disable** on startup ones that not used.
It is not deleting, you always can enable it back.

    Window -> Preferences -> General -> Startup and Shutdown

![](http://i.imgur.com/kgavA.png)

By the way, some plugins appeared due to your experimenting and not used anymore. **Delete** redundant plugins if they
not used at all.

    Help -> About Eclipse SDK -> Instalation Details -> <Select plugin> -> Uninstall

![](http://i.imgur.com/7wRoR.png)

#### eclipse.ini

Following tweaks based on editing `eclipse.ini` file located in root folder
of your eclipse instalation.

* **Use specific JVM to run eclipse.**
This allows do not take into account system properties which alters your JDK.

```
-vm
/path/to/your/java
```

* **Use latest JDK to run eclipse.**
Much better if you point your eclipse to the [latest JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
*Rumors* said it has better performance.

* **Use Sun JDK to run eclipse.**
The reason behind this has the same explanation.

* **Configure VM arguments.**
You can set your own values for virtual machine if you think
they are appropriate
([Performance related options](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html#PerformanceTuning))
I use following settings to increase heap size (objects) to `768Mb` and permgen (classes) to `256 Mb` on my `3Gb` RAM machine.

```
-vmargs
-Xms768m
-Xmx768m
-XX:PermSize=256m
-XX:MaxPermSize=256m
```

You can also add `-Xverify:none` that skips validation of loaded clases to JVM. Due to fact it skips one
step of JVM loading you will get performance boost. But it's very risky change.

You can also test your eclipse performance  with different Garbage collector strategies, server options,
experimental VM options like (some of these):

```
-server
-XX:+UnlockExperimentalVMOptions
-XX:+UseG1GC
-XX:+UseParallelGC
-XX:+UseFastAccessorMethods
-Xss2m
```

Check all [Eclipse runtime options](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.platform.doc.isv%2Freference%2Fmisc%2Fruntime-options.html)
for reference and choose ones that appropriate for you.

#### Disable animations

Animations it's cool. But I always disable animations if it possible in all tools.
Also, classic theme is most usable for me.

    Window -> Preferences -> General -> Appearance -> Uncheck 'Enable animations'

![](http://i.imgur.com/mzLp7.png)

#### Disable label decorations

Label decorations are small icons on project, file, class levels that helps
visually indicate some state of file. Like commited it to git or no.
They are provided by various plugins and rarely useful. Just left ones that you want.

    Window -> Preferences -> General -> Appearance -> Label Decorations

![](http://i.imgur.com/MKZp5.png)

#### Autocompletion

Sometimes on slow machines or when you have a lot of classes in your classpath autocompletion performance is terrible.
Small improvement you can get by reducing group of possible proposals. I left only `Java Proposals` and `Template Proposals`:

    Window -> Preferences -> Java -> Editor -> Content Assist -> Advanced

![](http://i.imgur.com/Aa7MY.png)

#### Uncheck validators

If you are sure in your competence just suspend all validators. If problem appears, you always
can identify the reason, but you save your development time.

    Window -> Preferences -> Validation -> Suspend All Validators

![](http://i.imgur.com/n1XQ2.png)

#### Close unrelated projects

If you working in context of few projects, better to have all unrelated projects closed. They won't appear
in index. You can manually `Close unrelated projects` in workspace. But I recommend to use `Working Set`.
You can add any number of projects to one working set, and quickly switch between different working sets
in one workspace.

    Right Click on Project -> Assign Working Sets..

#### Close unused tabs in editor

Big number of tabs in editor can greatly slow down your performance. Just limit them.

    Window -> Preferences -> General -> Editors

Check `Close editors automatically` and set `Number of opened tabs` to `10`.

![](http://i.imgur.com/gjRd8.png)

#### Disable spell check

Are you developer or what? I don't see any reason to have `spell check` enabled.

    Window -> Preferences -> General -> Editors -> Text Editors -> Spelling -> Uncheck 'Enable spell checking'

#### Disable autobuild

Probably, you aware about when you want to build your project and when not.

    Project -> Uncheck 'Build Automatically'
    Window -> Preferences -> Java -> Compiler -> Building -> Uncheck 'Scrub output folders when cleaning'
    Window -> Preferences -> Java -> Compiler -> Building -> Uncheck 'Rebuild class files modified by others'

#### Hotkeys

It is human-related advice. Even if you working in super-fast IDE and spend 10 actions to perform
single operation, your development process won't be fast. Configure most-often used actions
to hotkeys and learn them. You will get a positive feedback after a week of usage.

    Windows -> Preferences -> General -> Keys

To force myself learn all hotkeys I just disabled toolbar.

    Window -> Hide Toolbar

### Useful Links

* [http://wiki.eclipse.org/Eclipse.ini](http://wiki.eclipse.org/Eclipse.ini)
* [http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html)
* [http://www.beyondlinux.com/2011/06/25/speed-up-your-eclipse-as-a-super-fast-ide/](http://www.beyondlinux.com/2011/06/25/speed-up-your-eclipse-as-a-super-fast-ide/)
* [http://blog.normation.com/2010/05/24/optimizing-eclipse-performances/](http://blog.normation.com/2010/05/24/optimizing-eclipse-performances/)
* [http://stackoverflow.com/questions/142357/what-are-the-best-jvm-settings-for-eclipse/1409590#1409590](http://stackoverflow.com/questions/142357/what-are-the-best-jvm-settings-for-eclipse/1409590#1409590)

**P.S.** This article planned to be updatable, other tweaks are welcome!
