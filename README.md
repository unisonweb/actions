## @unisonweb/actions/stack/install
Installs `stack` and adds it to the path.

Example usage:
```
steps:
  - uses: unisonweb/actions/stack/install@main
    with:
      version: 2.9.1
```      
`version` is optional.

## @unisonweb/actions/stack/cache/restore
Restore the global and local stack work directories from a cache

Example usage:
```
steps:
  - name: restore stack caches
    id: restore-stack-caches
    uses: unisonweb/actions/stack/cache/restore@main
    with:
      cache-prefix: ci

  - name: check result
    if: steps.restore-stack-caches.outputs.cache-hit == 'true'
    run: echo "Found an exact match!"
    shell: bash
```

The caches are keyed by the cache prefix, the OS, and the resolver version in your `stack.yaml`, and the contents of your `stack.yaml` and `package.yaml`.

For a partial hit, only the cache prefix, the OS, and the resolver version must match, so you can use the `cache-prefix` input to distinguish between caches you wouldn't want to use as fallbacks, even if the OS and resolver match.

### Inputs
`cache-prefix`: The cache prefix to use for `~/.stack`, e.g. "release" or "ci". Required.
`work-cache-prefix`: The cache prefix to use for `**/.stack-work`. Defaults to the same as `cache-prefix`.
`stack-yaml-dir`: The directory to search for `stack.yaml`. Defaults to `"."`
`lookup-only`: If `'true'`, only checks if cache entry exists and skips download. Defaults to `'false'`. Note that due to technical limitations, these are strings, not Booleans.

### Outputs
`cache-hit`: Whether the `.stack` cache was restored with an exact match.
`work-cache-hit`: `'true'` or `'false'`, whether the `.stack-work` cache was restored with an exact match. Note that due to technical limitations, these are strings, not Booleans.

## @unisonweb/actions/stack/cache/save
Save the global and local stack work directories to caches.

Due to a technical limitation, these actions provide independent `restore` and `save` functionality. This is in contrast to the `github/actions/cache` action which automatically saves at the end of a succesful run, whenever there was not an exact cache hit during restore.

Instead, you have to do this manually: restoring at the start, and saving at the end whenever there was not an exact cache hit during restore.

Example usage:
```
steps:
  - name: save stack caches
    if: steps.restore-stack-caches.outputs.cache-hit != 'true'
    uses: unisonweb/actions/stack/cache/save@main
    with:
        cache-prefix: ci
```

### Inputs
`cache-prefix`: The cache prefix to use for `~/.stack`, e.g. "release" or "ci". Required.
`work-cache-prefix`: The cache prefix to use for `**/.stack-work`. Defaults to the same as `cache-prefix`.
`stack-yaml-dir`: The directory to search for `stack.yaml`. Defaults to `"."`
`lookup-only`: If `'true'`, only checks if cache entry exists and skips download. Defaults to `'false'`. Note that due to technical limitations, these are strings, not Booleans.
