# Reflection — Top 5 Lakehouse Anti-Patterns

**Most at risk: "ship the writer, skip the maintenance jobs."** (slide §6)

Our ingest commits every few seconds and nobody owns cleanup. NB2 and NB6
priced that. 200 small files over 100K rows meant 10M full-scan GETs/day —
.00/day in requests versus /bin/bash.08 compacted. Compaction cut 200 files to 11,
and Z-ORDER made 1 of 55 files answer a point lookup (55x pruning, 5.2x
wall-clock).

The part I did not expect: running the jobs is not the same as being clean.
Iceberg  dropped 20 snapshots to 3 and deleted **zero** avro
files; metadata grew (320.3 -> 327.2 KB). Only chaining an orphan sweep
reclaimed anything, and just 36.4 KB. On the Delta side VACUUM reported 211
reclaimable files while 5 crash-left orphans stayed invisible — the log never
tombstoned them, so no retention value finds them.

That is our exposure: run expiry, watch snapshots fall, declare storage fixed,
and the bill never moves. Jobs 3 and 4 are a pair. I am adding a set-difference orphan scan (disk minus log) to our
weekly maintenance, with an age guard so in-flight writers survive it.
