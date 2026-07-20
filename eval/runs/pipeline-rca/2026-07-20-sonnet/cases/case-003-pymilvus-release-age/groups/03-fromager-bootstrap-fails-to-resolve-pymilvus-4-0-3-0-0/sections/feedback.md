**Observation: Unversioned top-level requirement picks up a new major dependency boundary without detection**

`langchain-milvus` is listed without a version pin in the collection requirements. When `langchain-milvus 0.4.0` was released, it introduced a new transitive requirement for `pymilvus>=3.0.0` — a brand-new major version — that had no prior presence in this collection. The cooldown mechanism caught the issue, but only at bootstrap time after hours of processing.

**Expected:** A new top-level package version that introduces a new major-version transitive dependency would be flagged before a pipeline run, allowing the team to evaluate the readiness of that transitive dependency.

**Gap:** There is no automated check that detects when an unversioned collection requirement resolves to a newer version than the previously-built version, or that a newly-resolved version introduces a transitive dependency on a package with no existing build history in the collection. The failure was caught by the release-age cooldown (a reactive control) rather than a proactive check.

**Observation: Inconsistent error messages across architectures for the same root cause**

The x86_64 job produced a clear "release-age cooldown" message identifying the affected package and its age. The aarch64 job produced a "no match found" message with no mention of the cooldown. Both failures share the same root cause, but the error messages require interpretation to connect them.

**Expected:** Both jobs would surface the same structured error when failing due to the same mechanism.

**Gap:** The resolver appears to follow different code paths on aarch64 (or under different timing conditions) that produce a less informative error. An analyst without the x86_64 error for comparison would not immediately identify the cooldown as the cause from the aarch64 log alone.
