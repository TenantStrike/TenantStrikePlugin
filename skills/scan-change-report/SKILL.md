---
name: scan-change-report
description: Report what changed in a Microsoft 365 and Azure tenant between Tenant Strike scans, including new findings, findings that were fixed, and whether the grade moved. Use when the user asks what changed since the last scan or since last week, wants a weekly or monthly security review or a progress update, or asks whether a fix worked.
---

# Report what changed between scans

Compare the user's latest Tenant Strike scan with the one before it, read through the Tenant Strike connector.

## Steps

1. Call `get_scan_summary` with no arguments and keep `context.snapshot.scanRef`.
2. Call `get_changes_since_last_scan` with that `scanRef`.
   - If the status is `empty`, there is no earlier scan to compare with. Say so, give the current grade from the summary, and stop.
   - Otherwise `data.from` and `data.to` are the two scans, `data.changes` lists what differs, and `data.unchangedCount` is how many findings stayed the same.
3. Each change has a `change` value:
   - `new`: failing now, not before.
   - `resolved`: failing before, and the new scan has evidence that it is fixed.
   - `unassessed`: failing before, and the new scan could not measure it. This is not a fix.
   - `changed`: still failing, with different evidence or a different severity.
   - `check_revised` or `check_added`: Tenant Strike corrected or added the check between the two scans. The difference may come from the check and not from the tenant.
4. For the title, what was found and the fix, call `get_finding`. Use `data.to.scanRef` for `new`, `changed` and `check_added` rows, and `data.from.scanRef` for `resolved` and `unassessed` rows, because each row's `findingRefs` point into the scan the finding was read from. A `check_revised` row can point into either scan: try `data.to.scanRef`, and if the result says no finding matches, use `data.from.scanRef`. The fix is in `fix.summary`, with who does it in `fix.who`.
5. Call `get_posture_trend` with no arguments for the grade and score of each recent scan. If a row's `comparableWithPrevious` is false, the two scans did not measure the same things.

## What to write

- One line: the dates of the two scans, and the grade and score before and after.
- **New**: each new finding with its severity and what to do about it. Most severe first.
- **Fixed**: each resolved finding. If the user asked whether a specific fix worked, answer that first.
- **Could not be confirmed**: each unassessed finding, with a sentence saying it was not measured this time.
- **Changed**: anything else that moved.
- **The check changed**: each `check_revised` or `check_added` row, with its current result. Do not count these as fixed, new, better or worse.
- One closing sentence on the direction: better, worse or the same, and the next thing to do.

Keep it short enough to paste into a status update. Where a finding has a `reviewUrl`, add `[View evidence in Tenant Strike](reviewUrl)` with that exact URL.

## Keep the answer honest

- A finding that disappeared without evidence is `unassessed`. Never report it as fixed.
- If the two scans are not comparable, say that a change in the score is not a verified improvement or decline.
- A part named in a finding's `withheld` is text Tenant Strike holds and did not release here. Give the evidence link for it. A null part that is not named there is one the check did not write.
- The connector withholds names, email addresses and resource identifiers. Do not guess or invent them.
- If `datasetKind` is `synthetic` on either scan, say that this is sample data.
