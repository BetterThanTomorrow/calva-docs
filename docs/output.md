# The Output/REPL Window/File

When Calva evaluates Clojure/ClojureScript code, the results are displayed inline as well as printed to the results output window/file. This file is created and opened when Calva is connected to a REPL.

In ClojureScript projects the window will be associated with the `cljs` REPL once this one is connected. It will then look something like so:

![Output Window Connected](images/howto/output/output-window-connected.png)

The first prompt is from when the `clj` REPL is connected, the second when Calva has a `cljs` REPL connection. The first part of the prompt tells you which REPL type the window is currently connected to. This gets important when the file/window is used as an interactive REPL.

## Evaluating code

The window will be automatically associated with the REPL and the namespace of any project Clojure/ClojureScript file you evaluate code in. So for instance if you evaluate this code in a `clj` file with the namespace `fresh-reagent.handler`:

```clojure
(def mount-target
  [:div#app
   [:h2 "Welcome to fresh-reagent"]
   [:p "please wait while Figwheel is waking up ..."]
   [:p "(Check the js console for hints if nothing exciting happens.)"]])
```

The output window will print the defined var and then a new prompt reflecting the current REPL connection and namespace:

![eval-results-1](images/howto/output/eval-results-1.png)

If you then switch to the output window (`ctrl+alt+c o`), and enter this at the prompt:

```clojure
mount-target
```

then evaluate it using `alt+enter`, you'll get:

![eval-results-2](images/howto/output/eval-results-2.png)

This, since the namespace ”followed” the first evaluation over to the output window.

## Stacktraces

When an evaluation produces an error, the output window will automatically print the stacktrace (when available). It will be printed as an EDN structure. And when source locations are available you will be able to navigate to them by `command+click` (Mac) or `ctrl+click` (Windows and Linux).

## Find the Output/REPL Window

If you quickly want to open and switch to the output window there is the command **Calva: Show Output Window**, `ctrl+alt+c o`.

## Paredit Enabled

The output window is mostly a regular Calva Clojure/ClojureScript file, which make everything that works in a regular file work in this file, including [Paredit](paredit.md). This makes it easy to navigate the input and output. For instance, to select the last evaluation results you can press `ctrl+w`:

![Paredit select current form](images/howto/output/select-last-result.png)

## Debugger Enabled

The output window is mostly a regular... (you get it), which means you also have the [Calva debugger](debugger.md) at your command at the REPL prompt (only for `clj` sessions, so far). So instead of evaluating a function definition using `alt+enter` you can evaluate it and instrument it for debugging using `ctrl+alt+c i`. Then call the function.

![repl-file debugger](images/howto/output/repl-file-debugger.png)

## It is Ephemeral

The contents of the output/REPL window is written to a file named `output.repl-file` in the `.calva/output-window` directory of your project. The file is recreated at every new session. And you should copy anything you want to keep from this file to wherever you want to keep it.

You probably want to add `.calva/output-window/` to your `.<something>ignore` files. (There are some more files in that directory that you shouldn't keep under source control.)

## Choose CLJ or CLJS REPL Connection

In full stack projects, you will probably use the window as a REPL for both for `clj` and `cljs` project. You can toggle which REPL the window is connected to using the the command **Calva: Toggle REPL Connection for CLJC files**. There is a button for this in the status bar:

![Toggle CLJC](images/howto/cljc-toggle-button.png)

## Known Quirks

Due to limitations in the VS Code API it is hard for Calva to know if the output file is opened, and also if it is opened more than once. Make it a habit to leave this window opened. And if it is opened in several tabs, expect evaluation printouts to be a bit unpredictable.

If you save the output/REPL file (which most often does not make much sense, but anyway) you will sometimes be presented with a message about VS Code being confused about the file contents being out of sync. Just choose to *Overwrite* the currently saved version and you should be fine.