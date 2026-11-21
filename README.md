# RVS Access Admin

A page for CUSEC staff to manage **who sees which RVS records**: the **RVS Access** table
(item `994e5086fed6495dafec1c73f39b274d`) and the ArcGIS groups it points to, in the CUSEC Hub
community org. See `../rvs-access/README.md` for the table and the access rule.

Only members of **CUSEC RVS Management** (`47c04ff75e3d4635a9be3b10fd5f6897`) and organization
administrators can use it. Everything is done with **your own ArcGIS sign-in**; nothing is stored.

## What it does (first version)

**Audiences tab**: one row per state, county or private client from the RVS Access table:
- what records it covers, its group (with member count), its view and dashboard, and **how many RVS
  records match** (CWL's Craighead buildings are excluded from the Craighead and Arkansas counts);
- problem flags: no group/view yet, group or view not found, group with no members, no matching
  records, Client ID used twice;
- **Add / Edit** an audience: type, name, state/county or client name + Client ID (suggested from the
  name, locked once saved), group (pick one, or **create a new group** in the same step), view item ID,
  dashboard URL, active, notes. The form shows the **exact filter** to give that audience's view, and
  refuses duplicates (same records already covered, Client ID already used).
- Deactivate by setting **Active = No** (the row stays for history; it's hidden unless "Show inactive").

**Groups & members tab**: the org's groups (and which audience uses each), **New group**, and for a
selected group: its members, **search users** in the org, **Add** and **Remove**.

**Not yet (next version)**: creating the filtered **view** itself. For now, make it in ArcGIS Online
(RVS layer → *Create view layer*), paste the filter the app shows, share it only with the audience's
group, then put the view's item ID in the audience.

## Permissions it relies on
- Reading/writing the RVS Access table: CUSEC RVS Management (the table is shared with it and editable).
- Creating groups: your account's *create groups* privilege.
- Adding / removing **other people** in a group: an **organization administrator** (or the group's
  owner/manager for removals). Without it, ArcGIS refuses and the app says so.

## Setup
1. **ArcGIS app (done):** client ID **`7l8gKTdeHaruBBxx`** is set in `CONFIG.clientId` in `index.html`.
   Its redirect URLs must include `https://cusec-org.github.io/rvs-admin/` exactly (Hub community org →
   the app item → Settings → Redirect URLs).
2. **Publish**: repo `cusec-org/rvs-admin`, `index.html` at the root, Settings → Pages → branch `main`,
   folder `/ (root)` → `https://cusec-org.github.io/rvs-admin/`.

## Tested
Against a simulated ArcGIS backend (not the real org yet): non-admin refused; audiences list with
correct record counts and flags; edit + create group in one step; group member add / remove; user
search; duplicate-audience and duplicate-Client-ID checks; county/state input normalised.
