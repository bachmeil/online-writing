---
layout: post
title: Writing Geany plugins in D
---

One day in the not-so-distant past I was in the mood to take control of my text editor.

"That's easy. People have been using Emacs for that for decades."

Yep. I'm one of those people. I've used it for years. It's also a pain in the ass to have to sync my config file on all my machines.
You can't even open up a bare Emacs installation and edit a file.
It's hideous.
All the weirdness you could ever imagine.
I was thinking about something that doesn't feel like it was pulled from the set of Star Trek.

"Then use Textadept. You can go almost as far with that as with Emacs."

Yep. Unfortunately, not far enough. There's no version that will run on my ARM Chromebook.

My primary text editor these days is Geany.
I checked out the plugin documentation.
Turns out, it's not too bad.
It's set up to support writing C plugins.
That's good, because C is a subset of my favorite programming language, D, so if you can do it in C you can almost always do it in D.

Turned out to be rather easy. I wrote a plugin with basic outliner functionality.
Didn't get too deep into it.
It's not something anyone else would want to use.
I've got to say, it was enjoyable to write plugins for my text editor in D.

The main obstacle to spending more time on it is that it doesn't have a minibuffer.
A secondary obstacle is that I can't find a good way to set keyboard shortcuts inside my plugin.

Maybe some day in the distant future I will post my code so others can ditch Emacs and customize their text editor with D.