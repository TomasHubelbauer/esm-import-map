# ESM Import Map

In this repository I am playing around with ES Import Maps.

See the [GitHub Pages site](https://tomashubelbauer.github.io/esm-import-map).

Coming in, I had a few ideas in my head for how to (ab)use import maps:

- Self-test: run the entry-point script of the application, but map all scripts
  to their test/mock/stub/fake versions and let the application run all of its
  tests.
  Only then run it in normal mode.
  If the tests were fast enough or only some were cherry-picked, this would be a
  great feature for discovering potential breakage pro-actively.
- Backend toggle: add a UI toggle to dev builds that allows switching to what
  endpoints the ES module scripts resolve.
  Could be used for flipping between the TEST and STAG environments.
  Or even PROD and have a secret option for switching the PROD deployment to
  STAG in some hidden/auth-gated UI to be able to diagnose issues on the fly.
- "Test mode": this is similar to self-test, but instead of front-loading the
  tests, it would be possible to run them on-demand, maybe in response to user
  idle and then send any issues to analytics.
- Build-step free testing: again, very similar to the already stated ideas, but
  the trick here is that to run tests, only a cookie would be dropped or some
  other type of flag and the page would reload to the test runner mode of the
  app showing tests as they execute and then their results.

## Results

I found that dynamically inserting import maps on the fly is not possible! Even
using multiple import maps on one page doesn't work!

But… it is possible to insert an import map at runtime if done synchronously and
before any ES module script loads! That means using a normal type `script` to
make and insert that import map and then `script[type="module"]` which when
resolving module specifiers already has the import map available.

This means that any sort of interactive scenario is impossible. To switch import
maps, a page reload is always needed to be able to rerun the synchronous script
and have the ES module runtime restart.

How does this bode for the ideas coming in?

- Self-test is possible, but would have to work by running the tests, dropping a
  flag and then refreshing the page to the normal mode. Infeasible for user
  facing sites. Can't have double load.
- Backend switch: totally feasible, drop a cookie, refresh, check what backend
  the cookie says to point to, generate a corresponding map, use it. Easy!
- "Test mode": also feasible! Use `location.hash` and refresh or drop a cookie
  or similar and on load, check for this flag and run tests with the test import
  map, then refresh back to normal mode.
- Build-step free testing: possible but only though something like the above
  mentioned "Test mode", not so much as the self-test idea. Shame!

Overall, less impressive than I was hoping, but still usable for my crazy
experiments!

## Notes

### `file:`-based import map

Using a file-based import map is problematic when running off the `file:` scheme
even with CORS disabled for that protocol, because the import map referenced by
the `src` attribute must have the `application/importmap+json` MIME type, which
is not going to happen unless using a static file server which understands MIME
of import maps.

I am working around this by using an inline import map.

## Multiple import maps

Neither Firefox nor Chrome support merging multiple import maps at the time of
writing.

Firefox bug: https://bugzilla.mozilla.org/show_bug.cgi?id=1688879 (?)

Chrome bug: https://bugs.chromium.org/p/chromium/issues/detail?id=927119

## Scope patterns

The import maps spec also includes this idea of scopes which at the current
could be useful to make all files in `test/` import test versions of the main
application's references.

However I like my tests at `*.test.js` so scopes as they exist today won't help
there.
Someone already asked about patterns in `scopes` (and `imports`) here:

https://github.com/WICG/import-maps/issues/7
