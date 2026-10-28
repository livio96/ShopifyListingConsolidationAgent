# MEMORY.md

Read at the start of every run, update at the end. "Seeded" items come from the user's skill.

## Settings

- MODE_DEFAULT: audit
- MAX_GROUPS_PER_RUN: 10
- REPORT_EMAIL: (unset, no email is sent until you fill this in)
- PILOT_STATUS: not started   (set to "confirmed by user" after you check the storefront)

## Sweep cursor

- endCursor: (pass complete; next run starts again from the first page. Last page 29 ended at eyJsYXN0X2lkIjoxNTQyNTc1MzAyMjU0MywibGFzdF92YWx1ZSI6IjE1NDI1NzUzMDIyNTQzIn0=)
- passStartedAt: 2026-10-25 (pass 1 finished 2026-10-28; groups can straddle pages, links are resolved with nodes())
- lastFullPassAt: 2026-10-28

## Approved fixes (you edit this; the agent applies only these, then marks them done)

Format: `<MPN> | <fix types> | <note>`
Fix types: `status` (main ACTIVE, others UNLISTED), `links` (symmetric, hierarchy order), `condition`
(fill blank where SKU suffix is unambiguous), `content` (copy Overview/Specifications/FAQ from a sibling),
`group` (condition_group, mpn, related_family), `all`. Deletions are never applied from here.

Pending:
(none)

Done:
(none)

## Exceptions (skipped on purpose, not guessed)

Corrupted MPN keys (2026-10-27, skipped, not linked or touched):
- `6.11E+11`: 611247378014-N (Keurig frother, ACTIVE); 611247378007-N and -LN (Keurig K-Cafe, linked to each other, N ACTIVE, LN ACTIVE; both ACTIVE)

Blank condition or group (93 seen, all 0 stock, no links; 53 on pages 9-10, 40 more on page 11 on 2026-10-27: ACTIVE UTA2-750..UTA2-30000 (19), UTA3-250..UTA3-30000 (21)):
- UNLISTED: TA1-5/10/20/30/40/50/75/100/150, TA2-5/10/20/30/40/50/75/100/150/250/500/750/1000/1500/2000/2500/3000, TA3-250/500/750/1000/1500/2000/2500/3000 (33)
- ACTIVE: UTA1-10/20/30/40/50/75/100/150, UTA2-5/10/20/30/40/50/75/100/150/250/500 (20)

Duplicate condition values in one group:
(none)

Needs decision (2026-10-28, see outputs/2026-10-28-audit.md): 23 more `-RF` group keys; 2 more `-GEN`/New (26716-01-GEN, PWR-12V-2A-GEN); group keys with suffix 4J0A2AA-NOB, RE20-BLACK-N; CP-8851-NR-K9-LN has condition Refresh; 2200-48600-025RS has condition `Refurbished`; single-condition not ACTIVE: 1517-49585-001-N (UNLISTED), J9775A and J9773A-LN (DRAFT).

Needs decision (2026-10-27, see outputs/2026-10-27-audit.md): 31 Refresh-only products with `-RF` in condition_group; 14 `-GEN` SKUs with condition New; CP-PWR-CUBE-3-GEN possible split from CP-PWR-CUBE-3; SCMCTS30000-N group key SCMCTS3000.

LEAVE AS IS (your decision):
(none)

## Seeded: confirmed decisions (skill, updated 2026-10-13)

- One MPN = one group. Main = highest-ranked condition present, ACTIVE; all others separate, UNLISTED, linked.
- Hierarchy: New, New Open Box, Like New, Recertified, Refresh, Wholesale, Generic.
- Single-condition MPN: one ACTIVE product, no links.
- Variant merge was tried 2026-10-13 and reversed. Do not repeat without a new request.

## Seeded: reference groups (known-good, for the "check how it is already done" step)

- `84CEPY-PFC-1L1-C4` TI-84 Plus CE Python Rose Gold (fixed 2026-10-13).
- Pilot of 10 groups fixed 2026-10-13, storefront check by the user was pending: Garmin 010-02562-10,
  010-02562-11, 010-02785-00; Wahl 08504-308; Honeywell 1202G-2USB-5; Avaya 700504845; HP 7K2F3AA; TI-84 C8;
  TI-84 C10; Cisco DP-9800-KEM. C10 has no content anywhere in its group.

## Open questions (need you; the agent will not assume)

1. Cisco `Cisco Excess` / `REF` values: rename to Wholesale / Refresh?
2. 93 products have a blank condition or group (all TA*/UTA* 'Product Repair Upsell'): leave or classify?
3. Groups with no Overview/Specifications/FAQ anywhere: generate new content?
4. Digest email: set REPORT_EMAIL?
5. Did you check the 2026-10-13 pilot groups on the storefront?
6. STATUS RULE DISPUTE (2026-10-25): reference groups 84CEPY-PFC-1L1-C4 and 7K2F3AA have an in-stock lower condition ACTIVE and New (0 stock) UNLISTED; skill says New is always main. Hierarchy or stock-swap? No status fixes proposed until answered. 2026-10-26: 24 groups in pages 2-10 show the same pattern (list in outputs/2026-10-26-audit.md), 0 groups the opposite. Looks like stock-swap behaviour. 2026-10-27: one more (DP-9841-K9), 25 total. 2026-10-28: 10 more, 35 total, 0 opposite (list in outputs/2026-10-28-audit.md); includes pilot group 700504845 (Recertified ACTIVE, New UNLISTED 0 stock).
8. (2026-10-27) Refresh-only products keyed `<MPN>-RF` (31) and `-GEN` SKUs marked condition New (14): fix keys / conditions, or leave? Corrupted `6.11E+11` Keurig group: fix to real MPN?
7. Blank-condition products so far are TA*/UTA* families (53, see Exceptions). Leave? Should the 20 ACTIVE UTA* with 0 stock be live?

## Learned patterns (only what a run actually observed)

(none yet)

## Run log (newest first, one line each)

- 2026-10-28: audit, sweep pages 18-29, pass 1 COMPLETE: 564 products, ~440 groups, ~395 clean, ~45 flagged (10 status Q6, 3 single not ACTIVE, 23 -RF keys, 2 -GEN/New, 2 key suffix, 1 condition/SKU mismatch, 1 `Refurbished`), 0 link errors, no writes. Went past ~300-group guide to finish the pass. Reference group re-read: matches.
- 2026-10-27: audit, sweep pages 11-17: ~281 groups, ~234 clean, 47 flagged (1 status Q6, 31 -RF keys, 14 -GEN/New, 1 split, 1 key mismatch; see open question 8), 0 link errors, 40 blank + 3 corrupted skipped, no writes. Reference group re-read: matches.
- 2026-10-26: audit, sweep pages 2-10: 261 groups, 237 clean, 24 status-pattern (open question 6), 0 link/condition issues, 53 blank skipped, no writes. Reference group re-read: matches 2026-10-25.
- 2026-10-25: audit, sweep page 1 only: 33 groups, 0 link/condition issues, status rule discrepancy found (open question 6), no writes.
- 2026-10-25: Repo created. No Shopify run yet.
