**Observation: No retry for IncompleteRead during bootstrap**

The bootstrap process (fromager) does not retry when an HTTP connection is broken mid-transfer (`IncompleteRead`). While pip's internal urllib3 retry logic handles `RemoteDisconnected` errors (connection refused before data transfer begins), `IncompleteRead` during active data transfer propagates as a fatal error. This gap means that transient network glitches during package resolution or download — especially on longer-running bootstraps with hundreds of packages — can cause non-recoverable failures that require a full job retry.

Adding retry logic for `IncompleteRead` errors at the fromager level (or configuring urllib3's retry to cover read errors) would make the bootstrap process more resilient to transient network conditions.
