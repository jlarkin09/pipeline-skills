The cooldown expires ~24 hours after this pipeline run (pymilvus 3.x candidates are 2 days old; cooldown clears at 3 days). **The simplest resolution is to re-run the pipeline on 2026-07-21 or later** — no code change is required.

If an earlier unblock is needed, pin `langchain-milvus` to its previous version (0.3.3, which requires pymilvus 2.x) in the rhai collection constraints, then remove the pin once pymilvus 3.x clears the cooldown and the collection is ready to adopt it:

**File: `collections/rhai/cpu-ubi9/constraints.txt`**
```
langchain-milvus==0.3.3  # AIPCC-XXXXX: pin to avoid pymilvus 3.x cooldown; remove once cooldown clears
```

Apply the same constraint to all other rhai variant constraint files (`cuda12.9-ubi9`, `cuda13.0-ubi9`, `rocm7.1-ubi9`, `rocm7.14-ubi9`, `spyre-ubi9`), since `langchain-milvus` appears without a version pin in the `team-notebooks-images.txt` requirements for every variant.

**Alternatives**

**Option A: Pin pymilvus to a specific 3.x version (cooldown bypass).** If the team wants to adopt `langchain-milvus 0.4.0` immediately, pin pymilvus to a specific 3.x version (e.g., `pymilvus==3.0.0`) in the constraints file. A version-exact pin bypasses the cooldown check, as the resolver is no longer selecting from a range. This is riskier than waiting because the pymilvus 3.x release is untested in this pipeline.

**Option B: Wait for the cooldown to clear.** Re-run the pipeline on 2026-07-21 (the day after this run). No file changes needed. This is the lowest-risk path.

**Caveats**

- `langchain-milvus 0.4.0` also requires `milvus-lite<4.0,>=3.1.0`; verify that `milvus-lite 3.1.0` (released 2026-07-15) passes the cooldown before adopting `langchain-milvus 0.4.0`.
- If pinning to `langchain-milvus==0.3.3`, confirm that the prior version was successfully built in this pipeline before applying the constraint.
