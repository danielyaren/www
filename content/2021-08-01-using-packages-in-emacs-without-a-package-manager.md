+++
title = "Managing packages in Emacs"
description = "Managing packges in Emacs with git submodules and use-package"
[extra]
image = "favicon.ico"
header_color = "bg-near-white"
text_color = "purple"
alt_text_color = "black"
hover_color = "hover-purple"
+++

Emacs package managers are slow on Windows.

I'll show you how to manage packages as [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) and then configure them with [use-package](https://github.com/jwiegley/use-package).

We'll run over how to defer package loading with use-package to maintain a quick load time across Windows/macOS/Linux.

      

First, let's create a `lib` directory which we'll use to store our git submodules:

```
mkdir lib
```

then, let's bring in use-package

```
git submodule add https://github.com/jwiegley/use-package lib/use-package
```

this will add a reference to use-package in a newly created .gitmodules file.

All that's left is to bring in use-package in our emacs config:

```el
(defvar emacs-dir
  (eval-when-compile (file-truename user-emacs-directory))
  "The path to the .emacs.d directory. Must end with a slash.")

(push (concat emacs-dir "lib/use-package/") load-path)

(eval-when-compile
  (require 'use-package))
```

Now we're ready to start pulling in packages.

Let's bring in the [dash](https://github.com/magnars/dash.el) library:

```
git submodule add https://github.com/magnars/dash.el lib/dash
```

Because we're using git submodules, whenever we configure a package we need to set the load-path:
```el
(use-package dash
  :load-path "lib/dash"
  :defer)
```

<sub><sup>I like to add `:defer` here when configuring libraries like dash, request, s etc.</sup></sub>

For a simple package like dash, that's it.

Let's add another package that requires more configuration.

```
git submodule add https://github.com/abo-abo/ace-window lib/ace-window
```

```el
(use-package ace-window
  :load-path "lib/ace-window"
  :commands ace-swap-window
  :config (setq aw-keys '(?a ?s ?d ?f ?g ?h ?j ?k ?l))
  :bind ("C-x o" . ace-window))
```
<sub><sup>The `:commands` keyword will create an autoload for the `ace-swap-window` function, defering loading of it until used.</sup></sub>

Rinse and repeat for each package you want to use. Some packages require a bit of trial and error, so I like to check their source code and see what other packages they may depend on.

### Removing a package

To remove a package we need to carry out 3 steps:

+ Remove the package directory from `lib`
+ Remove its reference in .gitmodules
+ Remove the package directory from `.git/modules/lib/`

### Setup a fresh install

Just run:
`git submodule update --init --recursive`

### Summary

Hopefully this post has given you ideas on managing your packages in Emacs without a package manager. While the existing set of package managers in the Emacs ecosystem are great, I prefer this approach due to its simplicity.


