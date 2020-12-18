---
layout: post
title: Adding Shortcuts To An App - Part Four
description: The first in a series of tutorials detailing how to add shortcut actions to an iOS app. In this one, we create the project and add our first action
category: tutorials
summary: The first in a series of tutorials detailing how to add shortcut actions to an iOS app. In this one, we create the project and add our first action
---

In the [last post]({% post_url /tutorials/2020-04-04-adding-shortcuts-to-an-app-1 %}) we looked at starting from scratch with a blank Xcode project and adding a basic Shortcuts action that made text uppercase.

In this post we’re going to dive deeper and look at different types of parameters including handling arrays, enums, nested parameters and calculated options.

We’re going to be creating a new Shortcuts action that will accept multiple files of any type and rename them with a formatted date (either prepended or appended). We’ll also optionally offer to change the case and then output the files back into Shortcuts.

This project will be updated on [GitHub](https://github.com/mralexhay/ShortcutsExample) with each post.

Let’s get started!    

