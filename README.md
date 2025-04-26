# webrvjc

github action for webr installable package repo production

We want to understand how to build r-wasm packages for use with webR
and how to test them.

A basic objective is to define how to make a package that is "personally compiled
from source" available to a locally running webR.

## Using the action in this repo to build an artifact.tar

The packages in the root text file `packages` will be compiled
using the workflow (slightly modified from that at `https://github.com/r-wasm/actions/`)

Once the action has completed successfully, find the URL for github-pages.zip from
the action run log.  Unzipping it locally will produce artifact.tar

## Setting up the library for installing packages in webR

One approach is to set up a local static server.

The following R code can be used from a folder with
subfolder repo to which the src and bin folders in artifact.tar
have been extracted.  (I.e., repo/bin/emscripten/contrib/... exists)

```
library(httpuv)
runServer(
  host = "127.0.0.1", port = 8080,
  app = list(
    staticPaths = list(
      "/" = staticPath(
        "repo",
        headers = list("Access-Control-Allow-Origin" =  "*")
        )
      )
    )
  )
)
```

Then, within webR, `repos="http://localhost:8080"` can be used with
`webr::install` to install any of the packages in the `packages` folder, **provided**
all "required" dependencies are present.  See the notes.txt in this repo for
a sequence of installation commands that got us to the point that SummarizedExperiment
could be used.
