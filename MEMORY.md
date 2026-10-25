# MEMORY.md

Read at the start of every run, update at the end. "Seeded" items come from the user's skill.

## Settings

- MODE_DEFAULT: audit
- MAX_GROUPS_PER_RUN: 10
- REPORT_EMAIL: (unset, no email is sent until you fill this in)
- PILOT_STATUS: not started   (set to "confirmed by user" after you check the storefront)

## Sweep cursor

- endCursor: (none)
- passStartedAt: (none)
- lastFullPassAt: (none)

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

Corrupted MPN keys:
(none)

Blank condition or group:
(none)

Duplicate condition values in one group:
(none)

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
2. About 93 products have a blank condition or group: leave or classify?
3. Groups with no Overview/Specifications/FAQ anywhere: generate new content?
4. Digest email: set REPORT_EMAIL?
5. Did you check the 2026-10-13 pilot groups on the storefront?

## Learned patterns (only what a run actually observed)

(none yet)

## Run log (newest first, one line each)

- 2026-10-25: Repo created. No Shopify run yet.
