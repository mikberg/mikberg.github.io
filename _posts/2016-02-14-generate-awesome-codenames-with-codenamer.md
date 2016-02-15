---
layout: post
title:  "Give Your Releases Awesome Names with Codenamer"
date:   2016-02-14 12:36:01 +0000
categories: awesome projects
heroimg: images/ekorn.jpg
---

**TL;DR: I've released [Codenamer][codenamer], a simple CLI to generate codenames. Such as it's own release codename, `0.3.0 canonical-catastrophe`.**

Using codenames for releases has been one of my hobbies, ever since I started using Angular, inspired by their [cool-sounding and mysterious codenames][angular-codenames].

Using names for releases in large-ish projects can make them easier to remember and to talk about. Even if the name is totally unrelated to the contents of a release, it can be easier to relate a feature set to a name than to an arbitrary number.

In order to be extra cool, codenames should:

 - Sound awesome
 - Keep to a theme
 - Fulfill a set of rules, e.g. `<adjective>-<noun>`
 - Optionally relate to the release number, e.g. start with `a` for `0.1.x`.
 - [Alliterations][alliterations] are welcome.

## Using Codenamer

Codenamer lets you generate these memorable codenames without need for imagination. You just pipe it a (fairly large) body of text as input, and some rules for the codenames.

```sh
$ curl -s https://en.wikipedia.org/wiki/Alliteration | codenamer --count 10 --format cJ,n15-cN,a,n20
persuasive-parallelism
specific-syllables
symmetrical-syllable
consonant-consonants
alliterative-argument
pickled-proposition
special-speaker
rhetorical-release
memorable-meter
potential-pairs
```

The `format` option lets you specify the number of words per codename, the length of each word (or the combined length), the word class for each of the words and alliteration.

The module also has a Node API, so you can generate codenames as part of a release script or something.

Install it with `npm install --global codenamer`, and you're ready.

Check the repo at [mikberg/codenamer][codenamer].

[angular-codenames]: https://github.com/angular/angular.js/blob/master/CHANGELOG.md
[codenamer]: https://github.com/mikberg/codenamer
[alliterations]: https://en.wikipedia.org/wiki/Alliteration
