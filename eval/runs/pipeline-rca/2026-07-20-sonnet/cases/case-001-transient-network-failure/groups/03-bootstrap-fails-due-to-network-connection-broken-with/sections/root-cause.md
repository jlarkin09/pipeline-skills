The bootstrap process failed due to a transient network disruption on the aarch64 CI runners. During package dependency resolution, HTTP connections to the upstream package index were severed mid-response, causing Python's urllib3 to raise an `IncompleteRead` exception. This exception was not caught by the bootstrap tool (fromager), so it propagated as a fatal error.

**Failure Chain**

1. The bootstrap process was resolving package dependencies by querying remote PyPI/GitLab package indexes over HTTP.
2. A network disruption on the aarch64 runner infrastructure caused the HTTP response to be truncated mid-transfer.
3. urllib3 raised `IncompleteRead` (a subclass of `HTTPError`), which is not covered by pip's built-in retry logic that handles `RemoteDisconnected` errors.
4. The fromager bootstrap process treated this as a fatal error and exited immediately.

**Supporting Evidence**

The two jobs failed at different bootstrap progress points — 77% (247/317 packages) for the cpu variant and 84% (499/594 packages) for cuda12.9 — and with different byte counts (228 KB vs 1.1 MB). This non-determinism confirms the failures are tied to transient network conditions rather than a specific package or deterministic build state. Other aarch64 bootstrap jobs for smaller collections succeeded in the same pipeline run, which is consistent with a transient issue: longer-running bootstraps for larger collections like `rhai-innovation` make more network requests and have a higher probability of encountering a transient failure window.
