# Data Strategy

## Goal
Define a practical data plan for Learn++ experiments using:
1. One public benchmark dataset.
2. One medical imaging dataset, prioritizing NIfTI and BIDS.

## Tracks
1. Public benchmark track
   - Purpose: fast sanity checks for experiment flow and metric consistency.
   - Target characteristics: clear labels, stable train/val/test split, easy reproducibility.
2. Medical imaging track (NIfTI and BIDS first)
   - Purpose: domain-relevant evaluation for project goals.
   - Target characteristics: documented labels, usable metadata, legal reuse clarity.

## Dataset Selection Criteria
1. Label quality and class balance.
2. License and academic usage permission.
3. Ease of split definition and reproducibility.
4. Compute feasibility under available hardware.
5. Metadata quality for batch creation (site, session, scanner, acquisition details).

## Suggested First Pair (Planning Stage)
1. Public benchmark candidate
   - Choose one compact classification dataset suitable for quick baseline comparisons.
2. Medical candidate
   - Choose one NIfTI/BIDS dataset with clear classification labels and documented metadata.

## Learn++-Ready Data Contract
Use these fields in a shared data index file:
1. sample_id
2. dataset_name
3. source_track (public or medical)
4. label
5. split (train, val, test)
6. batch_id (D1, D2, ...)
7. modality
8. format (nifti or bids)
9. site_or_scanner
10. notes

## Batch Design Guidance
1. D1 should include stable base classes with clean labels.
2. D2 and later should introduce either new samples, harder cases, or new classes.
3. Keep each batch documented with class distribution and known caveats.
