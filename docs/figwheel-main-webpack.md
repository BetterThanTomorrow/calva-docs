# How to Use Calva with figwheel-main, deps and webpack


<!-- [Figwheel-main](https://figwheel.org/docs/) Figwheel is famous for introducing hot reloading to the clojurescript world. Figwheel-main is its latest incarnation - a step up from the original figwheel which is still available. -->

The advantage of using webpack over previous techniques such as `cljsjs` is that you can access all NPM libraries without the complexities of cljsjs, foreign-libs etc. Webpack should also do a better job of compressing and bundling those libraries for production as it should only include code you use rather than a whole cljsjs package.

---
## Setting up figwheel-main
Figwheel has [excellent docs](https://figwheel.org/docs/). Read those first - you will suffer if you don't. They include instructions for both a leiningen and a deps build, but we will concentrate on deps here. 

@PEZ has written a [fresh-figwheel-main](https://github.com/PEZ/fresh-figwheel-main) repo which will get you started. This already combines Calva, figwheel-main in a deps build. When setting this up, pay attention to the README at [step 3](https://github.com/PEZ/fresh-figwheel-main/blob/master/README.md). 

An alternative starter is the [figwheel.main.template](https://github.com/bhauman/figwheel-main-template), choosing the clj-new option for a deps build.
<!-- 
As we are going to add webpack to the mix, it's a good idea to build fresh-figwheel-main both with and without picking an alias to understand the difference. One route takes you to the Calva Jack-in repl, and the other drops you into the figwheel terminal repl. Currently we need the `fig` alias for the webpack build, but once this is built, we want the convenience of the Jack-in route for an in-line repl. -->

Other basic docs that you should be familiar with are [the clojure Deps and CLI guide](https://clojure.org/guides/deps_and_cli), and the
[Clojurescript quickstart](https://clojurescript.org/guides/quick-start#clojurescript-compiler).

## Key configuration files
* `deps.edn` - List dependencies, aliases and paths. :extra-deps and :extra-paths can be added into alias option maps for individual build configuration.
* `figwheel-main.edn` - These parameters may also be configured using metadata in the build. You will probably want to adjust
    - `:watch-dirs` - a vector of folder paths you want figwheel to watch for changes
    - `:css-dirs` - if you also want figwheel to hot reload on CSS updates
    - `:target-dir` - this determines where the compiler will place runtime javascript outputs - see below.
    - `:autotesting` - set this to true to enable the figwheel test endpoint /figwheel-extra-main/auto-testing in your app.
* `xxx.cljs.edn` - is the name of the configuration file used for build `xxx`.
* `dev.cljs.edn` - This configures the 'dev' build.
    * Optionally, you can add figwheel-main.edn options as metadata attached to the main map here. Useful if these change between builds
    * `:main` should contain your main namespace.
* `test.cljs.edn` 
    * `:main` should point to your test driver.

## Check things work without webpack
Clone [fresh-figwheel-main](https://github.com/PEZ/fresh-figwheel-main/blob/master/deps.edn) first. Use Calva Jack in and check that the app launches at localhost:9500. In a separate tab you should see the tests running at localhost:9500/figwheel-extra-main/auto-testing for a nice heads-up display with test notifications appearing in the editor. 


## Adding NPM modules
Read the [Using NPM](https://figwheel.org/docs/npm.html) guide. This documents how to use `:bundle` target to generate a webpack bundle. If you are using webpack, you will also be adding some NPM packages to your app. See [ClojureScript with Webpack](https://clojurescript.org/guides/webpack) for a rundown on how this works without figwheel or Calva.

For web apps, you will commonly want to include react libs as NPM module. NPM install them. Your `package.json` might look something like this:
```
{
  "dependencies": {
    "bootstrap": "4.5.0",
    "react": "16.13.0",
    "react-bootstrap": "1.2.2",
    "react-dom": "16.13.0"
  },
  "devDependencies": {
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11"
  }
}
```

## Configure a webpack build
We'll change the fresh-figwheel-main `build` alias to a webpack build.

Create a build configuration for it which has :target set to :bundle. This triggers creation of a webpack compatible bundle.
The figwheel docs suggest that the :bundle-cmd is optional, but I have not found it to be so. However, we can leave out the :output-to and :final-output-to keys as figwheel will insert its defaults into the command line there. 

```
^{:watch-dirs ["test" "src"]
  :css-dirs ["resources/public/css"]
  :auto-testing true
   }
{:main fresh-figwheel-main.core
 :target :bundle
 :bundle-cmd {:none ["npx" "webpack" "--mode=development" :output-to "-o" :final-output-to]}}
```

## Reconfigure deps
We can now exclude the cljsjs packages as these will be brought in from npm-modules in the webpack bundle.
Notice also that the `:build` alias now targets the "build" build.
```
{:deps {org.clojure/clojure {:mvn/version "1.10.0"}
        org.clojure/clojurescript {:mvn/version "1.10.773"}
        reagent {:mvn/version "1.0.0-alpha2"
                 :exclusions [cljsjs/react
                              cljsjs/react-dom
                              cljsjs/react-dom-server]}}
 :paths ["src" "resources"]
 :aliases {:fig {:extra-deps
                  {com.bhauman/rebel-readline-cljs {:mvn/version "0.1.4"}
                   com.bhauman/figwheel-main {:mvn/version "0.2.11"}}
                 :extra-paths ["target" "test"]}
           :build {:main-opts ["-m" "figwheel.main" "-b" "build" "-r"]}
           :min   {:main-opts ["-m" "figwheel.main" "-O" "advanced" "-bo" "dev"]}
           :test  {:main-opts ["-m" "figwheel.main" "-co" "test.cljs.edn" "-m" "fresh-figwheel-main.test-runner"]}}}
```
## Calva Jack-in to start the build
If you now Jack-in and select BOTH `fig` and `build` the `build` alias will run `figwheel.main`. 
Notice that it runs the compilation and then runs webpack, creating final artefacts at `target/public/cljs-out/build/main_bundle.js` and `target/public/cljs-out/build/main_bundle-auto-testing.js`.

The Jack-in will not complete in the output.repl-file window as the figwheel REPL has taken over at this point. So don't expect the nice Calva REPL commands to work yet.

```
> Executing task: clojure -Sdeps '{:deps {nrepl {:mvn/version "0.6.0"} cider/cider-nrepl {:mvn/version "0.23.0"} clj-kondo {:mvn/version "2020.04.05"} cider/piggieback {:mvn/version "0.4.2"}}}' -A:fig:build <

2020-08-21 18:06:40.161:INFO::main: Logging initialized @4776ms to org.eclipse.jetty.util.log.StdErrLog
[Figwheel] Validating figwheel-main.edn
[Figwheel] figwheel-main.edn is valid \(ツ)/
[Figwheel] Compiling build build to "target/public/cljs-out/build/main.js"
[Figwheel] Successfully compiled build build to "target/public/cljs-out/build/main.js" in 3.891 seconds.
[Figwheel] Bundling: npx webpack --mode=development target/public/cljs-out/build/main.js -o target/public/cljs-out/build/main_bundle.js
[Figwheel] Outputting main file: target/public/cljs-out/build/main-auto-testing.js
[Figwheel] Bundling: npx webpack --mode=development target/public/cljs-out/build/main-auto-testing.js -o target/public/cljs-out/build/main_bundle-auto-testing.js
[Figwheel] Watching paths: ("test" "src") to compile build - build
[Figwheel] Starting Server at http://localhost:9500
[Figwheel] Starting REPL
Prompt will show when REPL connects to evaluation environment (i.e. a REPL hosting webpage)
Figwheel Main Controls:
          (figwheel.main/stop-builds id ...)  ;; stops Figwheel autobuilder for ids
          (figwheel.main/start-builds id ...) ;; starts autobuilder focused on ids
          (figwheel.main/reset)               ;; stops, cleans, reloads config, and starts autobuilder
          (figwheel.main/build-once id ...)   ;; builds source one time
          (figwheel.main/clean id ...)        ;; deletes compiled cljs target files
          (figwheel.main/status)              ;; displays current state of system
Figwheel REPL Controls:
          (figwheel.repl/conns)               ;; displays the current connections
          (figwheel.repl/focus session-name)  ;; choose which session name to focus on
In the cljs.user ns, controls can be called without ns ie. (conns) instead of (figwheel.repl/conns)
    Docs: (doc function-name-here)
    Exit: :cljs/quit
 Results: Stored in vars *1, *2, *3, *e holds last exception object
[Rebel readline] Type :repl/help for online help info
Opening URL http://localhost:9500
```
## Edit the HTML to reference the bundled js

http://localhost:9500 is probably showing a blank white page and the console will be saying that "react" is undefined.
That's because the html is still pointing at the unbundled output which no longer includes react as we excluded it. 

Edit `target/public/index.html` to reference `<script src="cljs-out/build/main_bundle.js"></script>`, and all should now work

## Testing
Furthermore the autotest end-point should be working too.

## Using the Calva REPL
Building the webpack bundle broke the link with the CAlva REPL. Fortunately, the webpack bundle references the standard output main file. In development it's therefore only necessary to create the webpack bundle once. You can simply reconnect using a Calva Jack-in that does not call the figwheel main repl.

Jack-in and ONLY select `fig` this time, NOT `build`. In deps, the `fig` alias does not contain a call to figwheel.main.
Wait for the second Calva prompt. This time, select `build`. The Jack in will complete in the Calva context.

Nice - we have the Calva inline REPL experience running on a webpack bundle :) 


