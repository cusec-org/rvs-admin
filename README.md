# RVS Access Admin

A page for CUSEC staff to manage **who sees which RVS records**: the **RVS Access** table
(item `994e5086fed6495dafec1c73f39b274d`), the ArcGIS groups it points to, and each audience's
filtered **view** of the RVS layer, in the CUSEC Hub community org. See `../rvs-access/README.md` for
the table and the access rule. Live at **https://cusec-org.github.io/rvs-admin/**.

Only members of **CUSEC RVS Management** (`47c04ff75e3d4635a9be3b10fd5f6897`) and organization
administrators can use it. Everything is done with **your own ArcGIS sign-in**; nothing is stored.

## Why views

The apps filter records for convenience, but if a group can open the **full** RVS layer, its members
can still see every record (including private clients') in Map Viewer or the data table. A **view**
is enforced by ArcGIS itself: it only ever returns its audience's records. So every State, County and
Client group should open RVS data **only through its own view**, never the full layer.

## Setting up an audience: the wizard

**Add audience** (or **Finish setup** on any audience that isn't complete) walks through six steps.
Each step saves as it goes, so you can close it and pick up later.

1. **Who**: Client, County or State, and its name. The Client ID is made for you (locked once saved).
   The page says in plain words what this audience will see.
2. **Group**: create a new ArcGIS group (a name is suggested) or use an existing one.
3. **People**: search the organization and add the people who should see the records.
4. **Data view**: one button, **Create the view**: makes a read-only view of the RVS layer with this
   audience's filter, names it "RVS - *audience* (view)", shares it **only** with the group, and saves
   it on the audience. It shows how many records the view returns as a sanity check.
   If the group can still open the **full RVS layer** (true for the existing State and County groups
   today), it says so and offers **Stop sharing the full RVS layer with this group**. Do that **only
   after** that group's dashboard and apps use the new view, or its members lose access to their data.
5. **Dashboard** (optional): paste the audience's dashboard link (point the dashboard at the view).
6. **Done**: a checklist, with a **Fix** button next to anything still missing.

**Edit** opens a single form for quick changes (names, notes, Active). Set **Active = No** to retire
an audience (the row stays for history; hidden unless "Show inactive").

## Importing the existing State and County audiences

**Import existing audiences** reads every "CUSEC RVS … Experience" app and the groups it's shared with,
and proposes one audience per app (CUSEC RVS Management left out):
- "CUSEC RVS **AR** Experience" → **State** Arkansas, with **all** its groups: the state group and the
  agency group (e.g. AR State Group + ADEM RVS Group).
- "CUSEC RVS **Craighead County AR** Experience" → **County** Craighead, AR, with its county group. The
  county name comes from the county group, so "Cape County MO" becomes **Cape Girardeau**.
- The Experience link becomes the audience's dashboard. Record counts are shown to sanity-check.
- **"MS - County RVS Group" is left out** of Mississippi (its statewide group is **MEMA RVS Group**); that
  group is to be removed. Add any other group to leave out in `IMPORT_SKIP_GROUPS` in `index.html`.
Audiences already in the table are shown but not re-added. Nothing in ArcGIS changes except new rows.

## Several groups per audience

An audience can have more than one group (`GroupId` / `GroupName` hold comma-separated lists). The view
is shared with **all** of them, the full-layer check covers each, and the wizard's Group step lists them
with "Remove from this audience" and can add more. The People step has a group picker.

## The Audiences list

For each audience: what it covers, its group (and member count), its view and dashboard, **how many
RVS records match** (CWL's Craighead buildings are excluded from the Craighead and Arkansas counts),
and problem flags: no group / no view yet, group or view not found, view not shared with the group,
**group can still open the full RVS layer**, group with no members, no matching records, Client ID
used twice.

**Groups & members** tab: the org's groups (and which audience uses each), **New group**, and for a
selected group: its members, search users, **Add** and **Remove**.

## Permissions it relies on
- Reading/writing the RVS Access table: CUSEC RVS Management (the table is shared with it and editable).
- **Creating views** of the RVS layer: the **owner of the RVS layer** (item `307585a0…`) or an
  **organization administrator**. Others see a note saying who can do that step.
- **Stopping sharing** of the full RVS layer: its owner or an organization administrator.
- Creating groups: your account's *create groups* privilege.
- Adding / removing **other people** in a group: an **organization administrator** (or the group's
  owner/manager for removals). Without it, ArcGIS refuses and the app says so.

## Setup
1. **ArcGIS app (done):** client ID **`7l8gKTdeHaruBBxx`** in `CONFIG.clientId`; redirect URL
   `https://cusec-org.github.io/rvs-admin/`.
2. **Published:** repo `cusec-org/rvs-admin` → GitHub Pages (`main`, root).
3. `CONFIG.fullItems` lists the items that expose **all** RVS records (the RVS layer `307585a0…` and the
   read-only layer `70d1f24a…`). Add any other all-records item there so the full-layer check covers it.

## Tested
Against a simulated ArcGIS backend shaped like the real org (State/County groups sharing the full
layer): non-admin refused; flags for full-layer access; the CWL wizard from "no group" to done (group
created, member added, view created with `ClientId = 'jonesboro_cwl'`, shared only with the CWL group,
45 records); Arkansas: full-layer sharing removed from the Arkansas group only (CUSEC RVS Management
kept it); a new Tennessee audience end to end; duplicate checks; real-style save responses.
Import tested on the simulator loaded with the **real** 46 Experience apps and their sharing (read from
the org 2026-11-21): 44 new audiences added, states with both groups, MS with MEMA only, Cape Girardeau
named from its group; Missouri's view created and shared with both SEMA RVS Group and MO State Group.
On the real org: sign-in and loading the table and 87 groups. **View creation hasn't run on the real
org yet**; the first real run is the CWL view.
