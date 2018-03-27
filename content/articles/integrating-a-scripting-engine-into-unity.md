---
title: "Integrating a Scripting Engine Into Unity"
date: 2018-01-28T23:57:03Z
draft: true
tags: ["unity", "c#", "js"]
categories: ["unity"]
---

Unity is a 2d and 3d game engine that has recently been getting some long overdue
behind the scenes work. Programming is done exclusively in C# which, for the most part,
can handle whatever is thrown at it. But, being a compiled (vs interpreted) language,
it can impose limits. One example of the limitations imposed by Unity's choice of
language is the ability to write or edit code at runtime. If, for example, you'd like
to bestow upon the player the power of programming, a la [OpenComputers](https://minecraft.curseforge.com/projects/opencomputers),
a solution is to integrate a script interpreter into your program. But, this raises a
few problems. Up until recently, Unity only supported .NET 3.5 which severely limited
our options but, thanks to the aforementioned work (using Unity 2018.1) we suddenly have
a number of options, my favourite being [jint](https://github.com/sebastienros/jint).

### The .NET trap

The most important step in this process is to change the unity project to take advantage of
.NET 4.6, as many (read most) modern libraries simply don't target .NET 3.5 (which is almost)
10 years old by this point. Luckily this is easy, and has been supported since unity 5.5. To
change the version for the current project, go to edit -> project settings -> player and
change the API compatibility level to 4.6.

### NuGetting package management to play nicely

The next hurdle is unity's wacky plugin system, stemming from the fact that the solution for
our code is generate on unity's side, and regenerated each time there are any changes. So,
if you decide to install a package from NuGet, when unity decides to regenerate the solution
that package will disappear. Unity, instead, will scan your assets folder for plugins and
automatically load them into the list of packages available in the solution when it generates it.

When you install packages in NuGet they are saved into a folder called packages in the project
root, which you must then manually copy into the plugins folder for unity to discover them. This
solution, while not pretty, is enough to make it work for the time being.

### Jint