# ESM Import Map

In this repository I am playing around with ES Import Maps.

See the [GitHub Pages site](https://tomashubelbauer.github.io/esm-import-map).

## Notes

### `file:`-based import map

Using a file-based import map is problematic when running off the `file:` scheme
even with CORS disabled for that protocol, because the import map referenced by
the `src` attribute must have the `application/importmap+json` MIME type, which
is not going to happen unless using a static file server which understands MIME
of import maps.

I am working around this by using an inline import map.

## To-Do

- [ ] Check this out in Chrome

  I am using an experimental flag in Firefox, maybe Chrome implements parts of
  the spec or unspecced behaviors which I won't see in Firefox.

  I can only do this on the GitHub Pages version of the site as I don't have
  Chrome configured to allow `file:` protocol CORS which is needed for ESM.
