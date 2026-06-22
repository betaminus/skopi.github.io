source "https://rubygems.org"

# Local preview/build. The site is plugin-free, so GitHub Pages' classic
# server-side build (which uses its own toolchain) renders it identically.
# Jekyll 4.x is used locally because the github-pages gem pins Jekyll 3.9,
# which cannot run on Ruby 3.2+ (removed `tainted?`/`csv`).
gem "jekyll", "~> 4.3"

# stdlib gems removed from Ruby's defaults in 3.4+, required by Jekyll deps
gem "csv"
gem "base64"
gem "bigdecimal"
