---
title: "Command Line Todolist"
date: 2017-12-31T00:43:33Z
draft: false
tags: ["todo", "productivity"]
categories: ["terminal"]
---

In trying to boost my command-line productivity I decided to drop [todoist](https://www.todoist.com) and pick up
something that is a little more automation friendly. Todoist hides many of its useful features behind
their premium subscription as well as abstracting away the user from the data. I wanted a simple tool that
would allow me to take control of my data as well as providing a simple interface for automation.

The tool I settled on was [todolist](http://www.todolist.site), which is a simple but expressive todo app. It is nicely color 
coded which makes it easy to see what is going on at a glance. 

{{< asciinema w6gcbvStjURtwbd04TDGgjB00 >}}

## The API:

Working with the API is quite simple. It has a few basic commands, most of which can be accessed by their first character
(the character in bold).

Command  | Purpose
:-------  | :-------
init     | Initialize the todolist in the current directory.
**a**dd  | Allows you to add todos, with various "tokens" such as groups, due dates and tags.
**l**ist | Allows you to list the todos, optionally by group or due date.
**c**omplete | Completes the todo with the given id.
**p**rioritize | Prioritizes the todo, and prints it in bold.
**e**dit | Allows you to change tasks after adding them.
**ac**   | Archives all the completed todos.
web      | Starts the built in todo server allowing you to add and remove todos in a familiar GUI.

They encourage you to archive complete todos at the end of the day, to be left with the outstanding ones when you start
your day tomorrow. 

## Some ideas to augment your workflow

### cron job

Since your todolist is managed over the command line, it can take advantage of all the benefits the unix philosophy
provide. One such thing is cron, a job scheduler that lets you run commands at given times or intervals.

```bash
# archive all completed todos at 4am 
# after week days (tues-sat morning)
0 4 * * 2-6 todolist ac
```

### online backup

Since all the data is in a single file, it is trivial to keep it on google drive, syncthing or some other equivalent 
cloud storage solution keep the data synced across multiple devices.

    mv ./.todos.json ~/googledrive/.todos.json
    ln -s ~/googledrive/.todos.json ./.todos.json

### piping with grep or jq

Searching or formatting data is made easy with linux pipes. Your todolist can be easily grepped like any other output.
Additionally, to get some stats about your todo list you could invoke the very useful [jq](https://stedolan.github.io/jq/).
For example, here's a simple command to see the total number of completed tasks. Adding these to your bash/zsh profile
makes it really easy to get an overview over your todolist when you open a new terminal session.


    cat ~/.todos.json | jq '. | map(select(.completed == true)) | length' | xargs echo "Tasks Completed:"

