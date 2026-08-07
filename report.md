# Sourcey Documentation Report: gin-gonic/gin

Author: RawNuke

Target: gin-gonic/gin v1.11.0-dev at commit 34dac209ffb6ef85cc78c5d217bbb7ad001d68fd
License: MIT
Sourcey version: 3.6.5
Adapter: godoc (native Go toolchain go1.26.5)
Public URL: https://rawnuke.github.io/gin-docs/
Page count: 9 HTML pages across 8 Go packages

## Coverage

The godoc build extracted 61 standalone functions, 58 types, and 236 methods
from the gin module tree (8 packages). The search index holds 570 entries.
Every exported symbol in the documented packages is represented.

## Maintainer-facing gaps

### 1. The examples directory is not part of the documentation surface

Gin ships an `examples/` directory with reference implementations for common
patterns (authentication, file upload, middleware, etc.). The godoc adapter
configures `packages: ["./..."]`, which matches the module's Go packages but
excludes the `examples/` directory because it is not a Go package (it is a
flat directory of standalone `.go` files with no `package` declaration in
the directory root). A maintainer adding this to the Sourcey build would
give ecosystem users working code alongside the API reference. The fix is to
add a secondary markdown tab that pulls the README sections linked from the
examples or to restructure examples as one or more importable Go packages.

### 2. No README, changelog, or guide content in the docs site

The current build has one tab, "Go API", with only godoc output. Gin has a
substantive README.md, CHANGELOG.md, and BENCHMARKS.md at the repository
root. None of these appear in the documentation site. A consumer arriving at
the index page sees only the aggregated API reference with no prose context
about what gin is, how to install it, or what changed between versions. The
fix is to add a "Guides" tab sourcing markdown("./README.md") and a
"Changelog" tab or inline changelog block from CHANGELOG.md.

### 3. Source links point to master, not the pinned commit

The Sourcey config sets `editBranch: "master"` but does not pin the source
links to the documented commit. This means source links in the godoc pages
resolve to the current HEAD of master, not to the commit that was actually
documented. If master moves forward after the docs are built, a reader
clicking a source link may land on code that no longer matches the
documented API. The fix is to set `sourceBasePath` or use a commit-pinned
source URL in the godoc source link options.

### 4. Missing packages from the sidebar

The gin module tree includes the `internal/json` package but it is listed as
`codec/json` in the source. The sidebar navigation shows 8 packages, but the
internal/bytesconv and internal/fs packages appear under their internal
paths rather than grouped under a logical "Internal" section. A maintainer
might want to reorganize the sidebar grouping or hide internal packages that
are not part of the public API contract.

### 5. The site is on a personal domain, not the project's own domain

The docs are hosted at rawnuke.github.io/gin-docs/, which is a personal
GitHub Pages domain. For a project with its own domain (gin-gonic.com),
maintainer adoption would require either moving the Sourcey build output to
the project's own documentation site or merging a PR that adds Sourcey to
the project's CI pipeline. Until adopted upstream, this site serves as a
reference build that proves Sourcey works against gin's source, but lacks
the project endorsement that would make it a credible canonical home.

## Commands to reproduce

```bash
git clone https://github.com/gin-gonic/gin.git
cd gin
git checkout 34dac209ffb6ef85cc78c5d217bbb7ad001d68fd
npm install sourcey
npx sourcey build -o dist
```

## Build receipt

The build produced 9 HTML pages, search-index.json (171KB, 570 entries),
llms.txt, llms-full.txt, sitemap.xml, sourcey.css, sourcey.js, and 8 OG
images. The build completed in 14.9 seconds on macOS arm64 with go1.26.5.
