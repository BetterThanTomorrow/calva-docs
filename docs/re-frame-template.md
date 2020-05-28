# How to Use Calva with the re-frame template

To make it easy to start the app created by the [re-frame template](https://github.com/day8/re-frame-template) add this Connect Sequence in the `.vscode/settings.json` of the project:

```json
    "calva.replConnectSequences": [
        {
            "name": "Leiningen -> shadow-cljs",
            "projectType": "lein-shadow",
            "cljsType": "shadow-cljs",
            "menuSelections": {
                "leinAlias": null,
                "leinProfiles": [
                    "dev"
                ],
                "cljsLaunchBuilds": [
                    "app"
                ],
                "cljsDefaultBuild": "app"
            }
        }
    ],
```

Then **Jack-in**.