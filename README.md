# The Calva Documentation Distillery

For [Calva](https://github.com/BetterThanTomorrow/calva) Documentation - [calva.io](https://calva.io).

[![Calva Logo](/docs/images/calva-64h.png)](https://github.com/BetterThanTomorrow/calva)

The documentation is built using [MkDocs](https://www.mkdocs.org) + [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) and are hosted by GitHub Pages.

## How to Update the Docs

Please feel invited to improve the Calva Docs! Like so:

0. Install MkDocs + Materials

    ```sh
    pip install -r requirements.txt
    ````

    (Or your preferred method from https://squidfunk.github.io/mkdocs-material/getting-started/)

    If you get an error with the pip install method above, you may have an older version of python. If you install python 3.7 and run the following command, it should work:

    ```sh
    python3.7 -m pip install -r requirements.txt
    ```

0. Run the MkDocs server:

    ```sh
    mkdocs serve
    ```

0. Visit http://localhost.8000
0. Edit docs. It's all Markdown. When you save, the changes will be reloaded in the browser.

Note: The MkDocs Markdown is a bit pickier than GitHub's, so make sure to use four indents, put an empty line before bulleted lists, and so on. The VS Code extension markdownlint helps with following the appropriate style. _And make sure to check the results at your local site._

Currently there is no CI, so once it looks fine at your local site, file the PR.

## Updating [calva.io](https://calva.io)

Only users with push access to this repo can do this:

```sh
mkdocs gh-deploy --clean
```
