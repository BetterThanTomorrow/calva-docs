# Test Runner

Calva provides commands that can make running Clojure tests easier.

!!! Note
    Since the test commands utilize cider-nrepl, they only work with Clojure, not ClojureScript. See [this issue](https://github.com/clojure-emacs/cider-nrepl/issues/555) for more details.

## Test Commands

Command | Shortcut | Description
--------|----------|------------
Run All Tests | `ctrl+alt+c shift+t` | Runs all tests
Run Failing Tests | `ctrl+alt+c ctrl+t` | Runs the tests that failed
Run Tests for Current Namespace | `ctrl+alt+c t` | Runs the tests for the current namespace. If not a `-test` namespace, tests for the current namespace plus its corresponding `<current-namespace>-test` namespace will be run.
Run Current Test | `ctrl+alt+c ctrl+alt+t` | Runs the test at the cursor. This includes a `defn` with a `:test` in its metadata, a `defn` defined in a `with-test`, and a `deftest`.

## Test on Save

You can enable the Calva setting "Test on Save" to have tests for the current namespace run on file save.