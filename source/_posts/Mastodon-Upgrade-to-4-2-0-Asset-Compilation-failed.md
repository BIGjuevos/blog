---
title: Mastodon Upgrade to 4.2.0 Asset Compilation failed
date: 2024-02-03 18:03:18
tags:
  - mastodon
  - compilation
---

## Why
Here I was trying to be a good Mastodon admin and upgrade the version. I was coming from the 4.0.x branch.

I encountered the following issues and sharing the direct solution as I had to piece together a few different
methods from my searches and no one had a direct answer for this.

### Issue 1
I was seeing this error while trying to `bundle install`:

```
Your bundle is locked to json-canonicalization (0.3.2) from rubygems repository https://rubygems.org/ or installed locally, but that version can no
longer be found in that source. That means the author of json-canonicalization (0.3.2) has removed it. You'll need to update your bundle to a version
other than json-canonicalization (0.3.2) that hasn't been removed in order to install.
```

### Issue 2
Also, this issue later while pre-compiling the web assets:

```
mastodon@mastodon:~/live$ RAILS_ENV=production bundle exec rails assets:precompile
Compiling...
Compilation failed:

mastodon@mastodon:~/live$ 
```

## How

### Issue 1 Solution
If you are temporary moving to 4.2.0 instead of a higher version where it is fixed (at least in 4.2.5 it's at 1.0.0),
you can move higher `json-canonicalization` in your `Gemfile.lock`:

When I moved it to `0.3.3` I was able to move forward with the upgrade, not sure if `1.0.0` works with < `4.2.5`.

```
    [...snip...]
    json-canonicalization (0.3.3)
    [...snip...]
```

### Issue 2 Solution
Turns out that this command was running out of memory. The base usage of my node is a little over `1G`. 
So, when webpack was running, it was running out of memory over my `2G` limit. Upping my memory to `4G`
solved the problem.

If you only see this:
```
Compiling...
Compilation failed:
`isModuleDeclaration` has been deprecated, please migrate to `isImportOrExportDeclaration`
    at isModuleDeclaration (/home/mastodon/live/node_modules/babel-plugin-lodash/node_modules/@babel/types/lib/validators/generated/index.js:2740:35)
    at PluginPass.Program (/home/mastodon/live/node_modules/babel-plugin-lodash/lib/index.js:102:44)
```
and nothing else, it's probably a red-herring. This is a deprecation warning... just a shame it's directly under
the heading of `Compilation Failed:`.