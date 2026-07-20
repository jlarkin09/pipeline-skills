`langchain-milvus` is listed without a version pin in `collections/rhai/cpu-ubi9/requirements/team-notebooks-images.txt` (and equivalent files for all other rhai variants). On this pipeline run, fromager resolved it to version 0.4.0 — the newest release on PyPI, published 2026-07-17. `langchain-milvus 0.4.0` is the first version to require `pymilvus<4.0,>=3.0.0` (pymilvus 3.x), breaking away from the pymilvus 2.x requirement used by all prior versions.

All available pymilvus releases satisfying `<4.0,>=3.0.0` were published approximately 2 days before the pipeline run — still within fromager's 3-day release-age cooldown, which prevents use of packages published within the last 72 hours to avoid picking up broken releases before they receive community scrutiny. Because no pymilvus 3.x release is old enough to clear the cooldown, resolution fails immediately.

**Failure Chain**

1. `langchain-milvus` (unversioned in collection requirements) resolves to 0.4.0 — the latest available, published 2026-07-17
2. `langchain-milvus 0.4.0` declares `pymilvus<4.0,>=3.0.0` as an install requirement
3. Fromager queries PyPI for pymilvus versions in that range; finds 2 candidates (x86_64) / no sdist match (aarch64)
4. On x86_64: candidates are rejected because the oldest is only 2 days old (cooldown threshold: 3 days)
5. On aarch64: no sdist is found matching `<4.0,>=3.0.0` — likely a timing or sdist-availability difference versus x86_64
6. Bootstrap fails for all architectures; the job exits with code 1

**Variant-specific differences**

The x86_64 job explicitly identifies 2 pymilvus candidates rejected by the cooldown. The aarch64 job reports "found no match" searching for sdists, suggesting the resolver did not find an sdist for any pymilvus 3.x version — possibly because the sdist had not yet propagated to the PyPI simple index when the aarch64 job ran, or because the resolver followed a different code path. Both errors share the same root cause: the pymilvus 3.x releases are too new for the cooldown to clear.

The two s390x and ppc64le jobs hit the 4 MB log limit before reaching the langchain-milvus resolution step, so their error was not captured; however, they were at approximately 85% bootstrap progress on the same collection and are expected to have failed at the same point.
