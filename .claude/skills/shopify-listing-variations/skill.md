---
name: "shopify-listing-variations"
description: "Audit and fix Shopify listings that group one MPN across conditions (New, Like New, Open Box, Recertified, Refresh, Wholesale, Generic): separate linked products, status, SKUs, metafields, import CSVs."
---

# Shopify Listing Variations

Use this when the user works on TelQuest/ATHQ Shopify listings where one MPN is sold in several conditions: building the group, fixing links, status or content, or generating an import CSV. Applies to a single SKU/MPN, a list of them, or the whole catalog.

## The model (confirmed by the user, updated 2026-10-13)

- **One MPN = one group.** The group key is the `Condition group (MPN)` metafield (`custom.condition_group`), which equals the MPN.
- **Condition hierarchy (decides which product is the main one):**
  1. New
  2. New Open Box
  3. Like New (used)
  4. Recertified
  5. Refresh
  6. Wholesale
  7. Generic (aftermarket, compatible, not OEM; rare)
- **The main product is the highest-ranked condition present in the group, and it is ACTIVE.** If there is no New, the next available condition in the list is the main. **If a higher-ranked condition arrives later, it takes over as main** (for example a New appears while Like New is main: New becomes active, Like New becomes unlisted).
- **Every other condition is its own separate product** with its own single variant and its own SKU. Status is **unlisted**. Shopify status is per product, which is why conditions are separate products and not variants of one product.
- **A part number with only one condition is a single product: always ACTIVE, no links.** This includes Recertified-only or Like New-only products, and any that are currently draft or unlisted. When another condition is added later, rebuild the group by the rules above.
- **No product holds another condition's variant or SKU.** Never duplicate a SKU across products.
- **Do not merge a group into one product with Condition variants** unless the user explicitly asks again. It was tried on 2026-10-13: it created a fourth active product with duplicate SKUs, no content, and double-counted stock, and it was deleted.
- **A non-New main keeps its own SKU** (-NOB, -LN, bare MPN, etc.) and its own Overview heading (`Overview of <title> | <Condition>`).
- **Generic (-GEN)** is the lowest-ranked member of the OEM group for that MPN. It is linked and unlisted like the others (it is the main only if it is the only condition).

### Conditions and SKUs

| Condition | SKU | Handle suffix | Notes |
|---|---|---|---|
| New | `<MPN>-N` | `-new` | Rank 1 |
| New Open Box | `<MPN>-NOB` | `-new-open-box` | Rank 2 |
| Like New | `<MPN>-LN` | `-like-new` | Rank 3 |
| Recertified | `<MPN>` (no suffix) | `-recertified` | Rank 4 |
| Refresh | `<MPN>-RF` | `-refresh` | Cisco only, rank 5 |
| Wholesale | `<MPN>-WS` | `-wholesale` | Cisco only, rank 6 |
| Generic | `<MPN>-GEN` | `-generic` | Aftermarket, rank 7 |

The base SKU/MPN may contain slashes or dashes, so keep it exactly as in the store.

### Per-product fields

- **Option1:** `Title` / `Default Title` (single variant). This is what the live store uses. Do not use `Condition` as the option name for separate products.
- **Condition metafield** (`custom.condition`): the condition name exactly (`New`, `New Open Box`, `Like New`, `Recertified`, `Refresh`, `Wholesale`, `Generic`). **If it is blank, the storefront shows the product title as the button label.**
- **Linked products** (`custom.linked_products`): every other product in the group, never itself, in hierarchy order: New, New Open Box, Like New, Recertified, Refresh, Wholesale, Generic (the main product is first because it is the highest rank). Links must be symmetric. In CSV they are handles separated by `; `. Through the API they are a `list.product_reference` of product GIDs.
- **Per-condition values:** SKU, price, `b2b_price`, `b2c_price`, cost, stock, barcode, images.
- **Shared content, copied from the main product (or from whichever product in the group has it):** Overview, Specifications, FAQ. The Overview first line is `Overview of <title> | <Condition>`, so change only the condition after the `|`. Key Features are inside the Overview text, and the separate `features` metafield is normally empty. FAQ can point to the same FAQ metaobject entries. Flag it if any FAQ answer mentions the condition. If a Specifications MPN value carries a condition suffix (for example `-RF`), use the plain MPN when copying it to another condition.
- Also keep `condition_group`, `mpn` and `related_family` the same across the group.
- Keep titles identical across the group and let the Overview heading and the picker show the condition.

## Workflow

1. **Read the live state through the Shopify connector, not just a CSV.** Search `sku:<MPN>*` with `search_products`, or use aliased `productByHandle` queries (the export has no product IDs). Read status, variant SKUs, stock and the `custom` metafields (condition, linked_products, and whether overview/specifications/faq exist).
2. **Audit the group** against the model. Look for:
   - extra products created by an import (handle without a condition suffix, or one product with several variants);
   - duplicate SKUs across products;
   - blank `condition` metafield, wrong, missing or out-of-order `linked_products`, self-links, or links to a different MPN;
   - missing Overview, Specifications or FAQ;
   - wrong status (main not active, others not unlisted, single-condition product not active);
   - blank `condition_group`, or a corrupted MPN (see pitfalls).
3. **Report before changing anything**: what is wrong, what you propose, and what is irreversible (deleting products). Keep it short.
4. **Apply through the connector** only after the user agrees. Do a pilot batch first and ask the user to check the storefront before the rest:
   - Call `graphql_schema` and `validate_graphql_codeblocks` before every mutation.
   - Status: `productUpdate` with `status: UNLISTED` or `ACTIVE`, aliased in one mutation for many products.
   - Links and content: `metafieldsSet` (max 25 metafields per call). Pass long text exactly as read.
   - Extra variants inside a product: `productVariantsBulkDelete`.
   - Duplicate products: `productDelete` is permanent. Do it last, only with explicit confirmation, and only after checking that the real stock lives on the other products.
5. **Verify** with a final read (`nodes(ids:[...])`): status, linked handles in order, condition, and that Overview, Specifications and FAQ exist. Tell the user to reload the storefront page. Do not claim a storefront check you did not do.

## Import CSVs

When the user wants an import file instead of live edits, or for new listings:

- Start from the store's **products_export CSV** (82 columns) so every column is preserved. Do not use the older 66-column template. It lacks Overview and other metafields, so imports never set them on new products. The export has no Specifications column, so specifications must be copied through the API.
- One row per product. Never put other-condition rows under the main product's handle. That makes them variants of the main product.
- Copy status, price and stock from the export, or from the live store. Do not reset stock. Do not set a status the user did not choose.
- Include only the products that need changes, plus their extra image rows.
- An import cannot delete variants or products. Those need the connector or the admin.
- Tell the user to tick **Overwrite existing products with the same handle**.
- Produce a short change report next to the CSV: product, issue, old value, new value.
- Verify the file before sending: column count, unique handles, linked handles exist, no self-links, links symmetric.

## Pitfalls seen so far

- **Excel corrupts MPNs** that look like numbers (for example `6.11E+11`). Skip those groups and report them. Never merge on a corrupted key.
- **Cisco naming:** `Cisco Excess` probably means Wholesale, and `REF` means Refresh. Confirm with the user before renaming.
- **Blank condition:** about 93 products have no condition or group. Do not guess. List them.
- **Wrong cross-link example:** Logitech 960-001034 linked to 960-001013. Check that links stay inside one `condition_group`.
- **Barcodes** in CSVs start with an apostrophe (`'840064409339`). The store strips it on import. Leave it unchanged.
- **Duplicate condition values** inside one group mean the group is wrong. Do not link it.
- **Stale handles:** some handles do not match the SKU (for example `-e8` handles with `-C8` SKUs). If the titles match, leave them.
- **Deleting variants or products** is the only irreversible step. Everything else here can be corrected by writing again.

## Open decisions (ask the user, do not assume)

- Cisco `Cisco Excess` / `REF` condition values: rename to Wholesale / Refresh?
- Products with a blank condition or group (about 93): leave or classify?
- Groups where no product has an Overview, Specifications or FAQ to copy: generate new content?

## Progress log

- 2026-10-13: TI-84 Plus CE Python Rose Gold (MPN `84CEPY-PFC-1L1-C4`) done first: merged duplicate deleted, Like New and Open Box unlisted, Overview, Specifications and FAQ copied from New.
- 2026-10-13: Pilot of 10 groups (23 products) done: Garmin 010-02562-10, 010-02562-11 and 010-02785-00, Wahl 08504-308, Honeywell 1202G-2USB-5, Avaya 700504845, HP 7K2F3AA, TI-84 C8, TI-84 C10, Cisco DP-9800-KEM. Status, links and missing content fixed. C10 has no content anywhere in the group (needs new content). Waiting for the user to check the storefront.
- 2026-10-13: Hierarchy rule updated (New, New Open Box, Like New, Recertified, Refresh, Wholesale, Generic; best condition present is main; a higher rank takes over as main; single-condition products always active). Groups with no New are now in scope.
