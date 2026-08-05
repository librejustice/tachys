# tachys 0.2.15, lazy hydration behind a feature

Backport of [leptos-rs/leptos#4438](https://github.com/leptos-rs/leptos/pull/4438) onto the published `tachys` 0.2.15 crate.

In 0.2.x, `AnyView` fills both `hydrate_from_server` and `hydrate_async` for every erased type under `#[cfg(feature = "hydrate")]`. Only lazy routes reach `hydrate_async`, so an app that mounts with `hydrate_body` and has no lazy route ships the async rendering path as dead code. On one such app that field cost 1.30 MB against 0.98 MB brotli, about 25 %.

The patch makes the two fields exclusive, the way leptos 0.9 does it: `hydrate_from_server` under `all(hydrate, not(lazy))`, `hydrate_async` under `all(hydrate, lazy)`. The `lazy` feature is off by default.

leptos 0.8 has no `lazy` feature forwarding to `tachys/lazy`, so lazy hydration against this fork means depending on `tachys` directly with `features = ["lazy"]`.

Usage:

    [patch.crates-io]
    tachys = { git = "https://github.com/librejustice/tachys", rev = "d71bd7442162751080e5ff63d41af45e9a699e75" }

The fix ships in leptos 0.9 (tachys 0.3), where the gate is upstream and lazy loading is a `leptos` feature. This fork serves 0.8 trees and is meant to be dropped on migration.

Everything outside that patch is the crate as published. MIT, copyright upstream.
