Fromager failed to resolve `pymilvus<4.0,>=3.0.0`, a dependency of `langchain-milvus 0.4.0`, because all available pymilvus releases satisfying the constraint were published within the 3-day release-age cooldown window. Two architectures surfaced different error messages for the same underlying cause.

**x86_64** (job 15424929597):

```error
L31025: 08:31:56 ERROR Unable to resolve requirement specifier pymilvus<4.0,>=3.0.0 with constraint None using PyPI resolver (searching at https://pypi.org/simple): found 2 candidate(s) for pymilvus<4.0,>=3.0.0 but all were published within the last 3 days (release-age cooldown; oldest is 2 day(s) old) because found 2 candidate(s) for pymilvus<4.0,>=3.0.0 but all were published within the last 3 days (release-age cooldown; oldest is 2 day(s) old)
```

**aarch64** (job 15425062679):

```error
L31028: 08:39:37 ERROR Unable to resolve requirement specifier pymilvus<4.0,>=3.0.0 with constraint None using PyPI resolver (searching at https://pypi.org/simple): found no match for pymilvus<4.0,>=3.0.0 using PyPI resolver (searching at https://pypi.org/simple), searching for sdists, ignoring pre-release versions because found no match for pymilvus<4.0,>=3.0.0 using PyPI resolver (searching at https://pypi.org/simple), searching for sdists, ignoring pre-release versions
```

The failure occurs after `langchain-milvus 0.4.0` is fully prepared and its install requirements are extracted — at that point fromager attempts to resolve the transitive `pymilvus` dependency and immediately fails.
