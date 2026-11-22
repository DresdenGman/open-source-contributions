# tornado #3855 — Allow header on 405 responses

> HTTP protocol compliance · 2 files · +67/−0 · merged November 17, 2026 PST (November 18 UTC)

## Outcome

[PR #3855](https://github.com/tornadoweb/tornado/pull/3855) added the `Allow` header to tornado's 405 responses. A 405 response must carry an `Allow` header listing the methods the resource supports (RFC 9110, section 15.5.6), but tornado's 405 responses had no `Allow` header at all. Maintainer `bdarnell` merged the PR with a "Thanks."

## Diagnosis and design

405 responses arise from two places: the unimplemented-method stub and the unknown-method check in `_execute`. Rather than patching each raise site, the header is built at the point every 405 path converges on — `RequestHandler.send_error`, which also clears any preset headers first. When the status code is 405, a small helper compares each entry of `SUPPORTED_METHODS` against the base-class stub to find the methods the handler actually implements, and sets `Allow` accordingly. Building the header in `send_error` means both 405 sources produce it, and a header preset earlier in the request cannot survive into the response by accident.

## Validation

The full suite passed 1540 tests against a baseline of 1535, with no new failures (112 environment skips on both sides). The new tests cover the `Allow` value for a GET-only handler across several unsupported methods, that non-405 errors carry no `Allow` header, and that a handler implementing more methods lists them all.

## What I learned

For a protocol-compliance gap, the durable fix sits at the single convergence point every path shares, not at each place the status code is raised. The header also has to be computed from what the handler actually implements — comparing against the base-class stub — rather than from a static list, or the compliance fix would itself misreport the resource's methods.

## Evidence

- [Issue #902](https://github.com/tornadoweb/tornado/issues/902)
- [Merged PR #3855](https://github.com/tornadoweb/tornado/pull/3855)
- Merge commit `5466398b1f6e619d8c68ef7fd3ee3c25bf18e83c`
