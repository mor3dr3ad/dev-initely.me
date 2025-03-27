+++
title = "Workspaces in spacemacs"
draft = false
+++

In order to utilize spacemacs as an IDE, we need some way of juggling workspaces. A workspace in spacemacs is nothing more than a certain layout of buffers that allows us to quickly jump between files. An additional plugin I like to use here is treemacs which gives us a nice treelike structure on the left.

One example of how I use this:

-   While organizing my blog and writing new posts I use my main org-mode file in one window - usually I do not have other windows open here.
-   But sometimes I need to work on a small project for a course at the same time or switch back and forth between the code and my org-mode file.

For this, I use the `eyebrowse` plugin. In order to switch between workspaces, you can simply use the keyboard shortcut `C-c C-w 1` to go to workspace 1 and `C-c C-w 2` to go to workspace 2. In these workspaces you can then arrange your buffers any way you like.

See the screenshot for an example where I have opened a couple of files pertaining to a project for CS 50 Web Course from Harvard. On the left side you see the treemacs tree, below you see my org-mode file where I keep track of requirements and so forth. Window navigation is done via `SPC 1`, `SPC 2` within a workspace. The great thing is you can even open the org-mode file in different locations in different workspaces.

{{< figure src="/ox-hugo/clipboard-20250326T170808.png" >}}


## Saving workspaces {#saving-workspaces}

TODO
