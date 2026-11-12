# MEMORY.md

Read at the start of every run, update at the end. "Seeded" items come from the user's skill.

## Settings

- MODE_DEFAULT: audit
- MAX_GROUPS_PER_RUN: 10
- REPORT_EMAIL: (unset, no email is sent until you fill this in)
- PILOT_STATUS: not started   (set to "confirmed by user" after you check the storefront)

## Sweep cursor

- endCursor: (pass complete; next run starts again from the first page. Last page 29 ended at eyJsYXN0X2lkIjoxNTQyNTc1MzAyMjU0MywibGFzdF92YWx1ZSI6IjE1NDI1NzUzMDIyNTQzIn0=)
- pass 2: started 2026-10-29, page 1 read, no changes since pass 1 (updated_at check = 0 products), stopped. Next run: re-check updated_at since 2026-10-29 first, full re-read only if products changed.
- pass 3 (2026-10-30): change check found 964 of 1,413 products updated since 2026-10-29T20:00Z. Only products 1-100 by ID re-read (ended at id 9045897445455, cursor eyJsYXN0X2lkIjo5MDQ1ODk3NDQ1NDU1LCJsYXN0X3ZhbHVlIjoiOTA0NTg5NzQ0NTQ1NSJ9, sortKey ID). Resume there; full re-read needs a script or several workers.
- pass 3 (2026-11-01): change check found 11 products updated since 2026-10-30T12:00Z, all audited (see outputs/2026-11-01-audit.md). Pass 3 re-read still only covers first 100 by ID; resume at the cursor above.
- pass 3 (2026-11-02): change check since 2026-11-01T12:00Z found 1 product, clean. Resume cursor unchanged.
- pass 3 (2026-11-03): change check since 2026-11-02T12:00Z found 5 products, all clean. Resume cursor unchanged.
- pass 3 (2026-11-04): change check since 2026-11-03T12:00Z found 9 products, 2 issues (see outputs/2026-11-04-audit.md). Resume cursor unchanged.
- pass 3 (2026-11-05): change check since 2026-11-04T12:00Z returned more than 100 products (bulk touch at 07:00Z today). First 100 by ID read, cursor eyJsYXN0X2lkIjo5MDc2MDY4NDgzMTUxLCJsYXN0X3ZhbHVlIjoiOTA3NjA2ODQ4MzE1MSJ9 (same query, resume there). 9 issues, see outputs/2026-11-05-audit.md.
- pass 3 (2026-11-06): resumed the 11-04 change-check query at the 11-05 cursor; 2 pages (103 products) read, hasNextPage false, so the >=2026-11-04T12:00Z set is fully read. Next run: change check since 2026-11-06T12:00Z. Full pass 3 re-read (first 100 by ID done, rest not) still not completed.
- pass 3 (2026-11-07): change check since 2026-11-06T12:00Z found 3 products, all clean/known. Next run: change check since 2026-11-07T12:00Z. Resume cursor for full re-read unchanged.
- pass 3 (2026-11-08): change check since 2026-11-07T12:00Z found 0 products. Next run: change check since 2026-11-08T12:00Z. Resume cursor for full re-read unchanged.
- pass 3 (2026-11-09): change check since 2026-11-08T12:00Z found 0 products. Next run: change check since 2026-11-09T12:00Z. Resume cursor for full re-read unchanged.
- pass 3 (2026-11-10): change check since 2026-11-09T12:00Z found 8 products, all clean. Next run: change check since 2026-11-10T12:00Z. Resume cursor for full re-read unchanged.
- pass 3 (2026-11-11): change check since 2026-11-10T12:00Z found 3 products, clean/known. Full re-read resumed at the 10-30 cursor (sortKey ID): products 101-600 read (~297 groups). Resume full re-read at eyJsYXN0X2lkIjo5MDc2MDY2ODExOTgzLCJsYXN0X3ZhbHVlIjoiOTA3NjA2NjgxMTk4MyJ9 (600 of 1,413, sortKey ID). Next run: change check since 2026-11-11T12:00Z, then continue the re-read.
- pass 3 (2026-11-12): change check since 2026-11-11T12:00Z found 0 products. Full re-read products 601-1050 read (~350 groups). Resume full re-read at eyJsYXN0X2lkIjoxMDMzMzc5MzU4MzE4MywibGFzdF92YWx1ZSI6IjEwMzMzNzkzNTgzMTgzIn0= (1,050 of 1,413, sortKey ID). Next run: change check since 2026-11-12T12:00Z, then finish the re-read (about 363 products left).
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

Needs decision (2026-11-12, see outputs/2026-11-12-audit.md): no ACTIVE product in C31CJ57112 (N 2, LN 4 in stock), IE-3200-8T2S-E, DP-9841-K9 (was RF ACTIVE); 2 more Q6 (SIP-T58W-PRO, 206110-101); 10 single UNLISTED 0 stock (82M90AA, ATA192-3PW-K9, 1107, DP-9861-K9, 2200-87010-025, C31CH51001, RZ01-03350100-R3A1, SIP-T46S, AIR-AP-BRACKET2, IE-3300-8T2S-A-RF); 2 more -GEN/New (CP-DX-HS-GEN-N, USBC-USBA-GEN); 11 more -RF keys.

Needs decision (2026-11-11, see outputs/2026-11-11-audit.md): no ACTIVE product in 77Y82AA-ABA (LN 2, N 2 in stock), 211203-01 (was LN ACTIVE), 2200-48830-001; single UNLISTED 0 stock: 210979-01-LN, 77Z31AA-LN, 8M3X3AA-LN, LOCOM5-EU-LN, ZT41143-T0E0000Z-N; EXM100-1000 condition New but bare SKU; 3 more Q6 (7W6D4AA-ABA, RZ04-05400100-R3U1, LI3678-SR3U42A0S1W).

Update 2026-11-10: HEGSKAAAAUSZ-LN (Nintendo Switch OLED) now ACTIVE (resolved).
Update 2026-11-07: DELL-WD25Z now has New ACTIVE (resolved).

Needs decision (2026-11-06, see outputs/2026-11-06-audit.md): no ACTIVE product in SR-A10 (N, LN both UNLISTED) and CBS250-48T-4X-NA (RF, LN both UNLISTED); 14 single-condition UNLISTED items listed in the report; 10 more -RF keys.

Needs decision (2026-11-05, see outputs/2026-11-05-audit.md): DELL-WD25Z has no ACTIVE product (N and LN both UNLISTED); single UNLISTED: 37966020-N, 1950GSR-2USB-LN.

Needs decision (2026-10-28, see outputs/2026-10-28-audit.md): 23 more `-RF` group keys; 2 more `-GEN`/New (26716-01-GEN, PWR-12V-2A-GEN); group keys with suffix 4J0A2AA-NOB, RE20-BLACK-N; CP-8851-NR-K9-LN has condition Refresh; 2200-48600-025RS has condition `Refurbished`; single-condition not ACTIVE: HEGSKAAAAUSZ-LN Nintendo Switch OLED (UNLISTED, 2026-11-04), 1517-49585-001-N (UNLISTED), J9775A and J9773A-LN (DRAFT).

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
6. STATUS RULE DISPUTE (2026-10-25): reference groups 84CEPY-PFC-1L1-C4 and 7K2F3AA have an in-stock lower condition ACTIVE and New (0 stock) UNLISTED; skill says New is always main. Hierarchy or stock-swap? No status fixes proposed until answered. 2026-10-26: 24 groups in pages 2-10 show the same pattern (list in outputs/2026-10-26-audit.md), 0 groups the opposite. Looks like stock-swap behaviour. 2026-10-27: one more (DP-9841-K9), 25 total. 2026-10-28: 10 more, 35 total (2026-11-01: 3 more, 38 total; 2026-11-04: UAP-AC-PRO-US, 39 total), 0 opposite (list in outputs/2026-10-28-audit.md); includes pilot group 700504845 (Recertified ACTIVE, New UNLISTED 0 stock).
8. (2026-10-27) Refresh-only products keyed `<MPN>-RF` (31) and `-GEN` SKUs marked condition New (14): fix keys / conditions, or leave? Corrupted `6.11E+11` Keurig group: fix to real MPN?
7. Blank-condition products so far are TA*/UTA* families (53, see Exceptions). Leave? Should the 20 ACTIVE UTA* with 0 stock be live?
9. (2026-10-30) 964 products updated 2026-10-29 evening and productsCount is 1,413 (this file said 564): expected? New in first 100: WD19S180W Q6 variant (Recertified ACTIVE, Like New UNLISTED 0 stock), 010-02564-00 and P2426H single-condition UNLISTED.

## Learned patterns (only what a run actually observed)

(none yet)

## Run log (newest first, one line each)

- 2026-11-12: audit, change check (0 products) + pass 3 re-read products 601-1050 (~350 groups, 0 link errors, 3 corrupted-key skipped): 3 groups with no ACTIVE (C31CJ57112 has stock), 2 more Q6, 10 single UNLISTED, 2 -GEN/New, 11 -RF keys. Reference group matches. No writes. Pass 3 at 1,050/1,413.
- 2026-11-11: audit, change check (3 products, clean/known) + pass 3 re-read products 101-600 (~297 groups, 0 link errors, 93 blank skipped): 3 groups with no ACTIVE, 5 single UNLISTED, 1 condition/SKU mismatch, 3 more Q6. Reference group matches. No writes. Pass 3 at 600/1,413.
- 2026-11-10: audit, change check: 8 products updated since 11-09 12:00Z, 8 groups, all clean (HEGSKAAAAUSZ-LN now ACTIVE). Reference group matches. No writes. Pass 3 not completed.
- 2026-11-09: audit, change check: 0 products updated since 11-08 12:00Z. Reference group 84CEPY-PFC-1L1-C4 re-read: matches (NOB ACTIVE, New and LN UNLISTED, links symmetric). No writes. Pass 3 not completed.
- 2026-11-08: audit, change check: 0 products updated since 11-07 12:00Z. Reference group 84CEPY-PFC-1L1-C4 re-read: links symmetric, NOB ACTIVE (2 in stock), New and LN UNLISTED (known Q6 pattern). No writes. Pass 3 not completed.
- 2026-11-07: audit, change check: 3 products updated since 11-06 (DELL-WD25Z-N now ACTIVE, Epson C31CH51011-LN single clean, SCMCTS30000-N known). Reference group matches. No writes. Pass 3 not completed.
- 2026-11-06: audit, change check finished the >=11-04 set (103 more products, ~85 groups, 0 link errors): 2 groups with no ACTIVE, 14 single UNLISTED, 10 -RF keys, Q6 MP56-TEAMS. Reference group matches. No writes. Pass 3 not completed.
- 2026-11-05: audit, change check: more than 100 products updated since 11-04 12:00Z (bulk touch 07:00Z), first 100 read (~75 groups, 0 link errors): DELL-WD25Z no ACTIVE, 4 more Q6 (1000970, 8X231AA, DS2278-SR7U2100PRW, ZD22042-D0EG00EZ; 43 total), 2 single UNLISTED. Reference group matches. No writes. Pass 3 not completed.
- 2026-11-04: audit, change check only: 9 products updated since 11-03 (7 clean, UAP-AC-PRO-US Q6 status, Nintendo HEGSKAAAAUSZ-LN single UNLISTED). Reference group matches. No writes. Pass 3 not completed.
- 2026-11-03: audit, change check only: 5 products updated since 11-02 (U6-PLUS, Zebra ZD4A042, SCMCTS3000, CP-8841-3PW-K9, NT8B26AABL), all clean/known. Reference group matches. No writes. Pass 3 not completed.
- 2026-11-02: audit, change check only: 1 product updated since 11-01 (Zebra ZD4A042-D0EE00EZ, clean). Reference group matches. No writes. Pass 3 not completed.
- 2026-11-01: audit, change check only: 11 products updated since 10-30, about 10 groups, 3 more Q6 status items (1013679, WPP30, CP-3905; 38 total), 1 single UNLISTED (SIP-T34W-LN). Reference group matches. No writes. Pass 3 not completed.
- 2026-10-30: audit, pass 3 partial: 964/1,413 products changed since 10-29, only first 100 re-read (about 70 groups, 0 link errors, 4 status items), full re-read not feasible in-context. Reference group matches. No writes.
- 2026-10-29: audit, pass 2: page 1 read (~33 groups, 32 clean, 1 known Q6 status pattern), change detection found 0 updated products since 2026-10-28, so no full re-read. Reference group re-read: matches. No writes.
- 2026-10-28: audit, sweep pages 18-29, pass 1 COMPLETE: 564 products, ~440 groups, ~395 clean, ~45 flagged (10 status Q6, 3 single not ACTIVE, 23 -RF keys, 2 -GEN/New, 2 key suffix, 1 condition/SKU mismatch, 1 `Refurbished`), 0 link errors, no writes. Went past ~300-group guide to finish the pass. Reference group re-read: matches.
- 2026-10-27: audit, sweep pages 11-17: ~281 groups, ~234 clean, 47 flagged (1 status Q6, 31 -RF keys, 14 -GEN/New, 1 split, 1 key mismatch; see open question 8), 0 link errors, 40 blank + 3 corrupted skipped, no writes. Reference group re-read: matches.
- 2026-10-26: audit, sweep pages 2-10: 261 groups, 237 clean, 24 status-pattern (open question 6), 0 link/condition issues, 53 blank skipped, no writes. Reference group re-read: matches 2026-10-25.
- 2026-10-25: audit, sweep page 1 only: 33 groups, 0 link/condition issues, status rule discrepancy found (open question 6), no writes.
- 2026-10-25: Repo created. No Shopify run yet.
