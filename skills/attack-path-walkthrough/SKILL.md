---
name: attack-path-walkthrough
description: Walk through the attack paths in a Tenant Strike scan, showing how separate weaknesses in a Microsoft 365 or Azure tenant chain into one attack and which fix breaks each chain. Use when the user asks about attack paths, how an attacker would get in, how their findings connect, what the worst case is, or which single fix matters most.
---

# Walk through the attack paths

Tenant Strike builds attack paths from the findings of one scan: a path names the weaknesses that, together, make a known kind of attack possible. Explain them from the user's saved scan, read through the Tenant Strike connector.

## Steps

1. Call `get_scan_summary` with no arguments and keep `context.snapshot.scanRef`.
2. Call `get_attack_paths` with that `scanRef`. Each path has:
   - a `title`, and a `route` that says the path in a few words, from the way in to the outcome
   - a `likelihood` and an `impact`
   - a `narrative`: the story of the path and what stops it
   - its `steps` in order. Each step has a `stage`, the MITRE ATT&CK `technique` with its `mitreId`, a `description` in plain words, and the `findingRefs` of the findings that make the step possible. A step with no `findingRefs` is what the attacker does next, not something the scan found.
   - `findingRefs`: every finding the path depends on
3. If there are no paths, say so, and say that this means none of the scenarios Tenant Strike looks for is supported by the findings. It does not mean the tenant cannot be attacked.
4. If `pagination.truncated` is true, call again with the same `scanRef` and `offset` set to `pagination.nextOffset` until you have the paths you need.
5. Take the paths the user asked about, or the three with the highest impact. For each finding a step depends on, call `get_finding` with the `scanRef` and the `findingRef` for the fix. Read at most four findings per path and say how many more there are.
6. Look for a finding that appears in more than one path. Fixing it weakens every path it appears in, so name it first.

## What to write

For each path:

- The `route`, or the `title` when there is no route, with its likelihood and impact.
- The steps in order, one short line each, from each step's `description`.
- The fix that breaks the chain: the finding whose fix is quickest or that the most paths share, with what to do from its `fix.summary` and who does it.
- Where a finding has a `reviewUrl`, add `[View evidence in Tenant Strike](reviewUrl)` with that exact URL.

Close with the one fix to make first across all the paths you covered, and why.

## Keep the answer honest

- Every path is a conditional scenario. Say "could" and "would let", not "did". The scan does not show that anyone has done this.
- Use each step's `description` as written. It is worded to say no more than the finding supports, so do not strengthen it: "may be enough" must not become "is enough".
- A step whose `description` is null has a sentence that was not released here, because it may name something in the tenant. It is shown in Tenant Strike. Describe the step from its `stage`, its `technique` and the findings it depends on, and say the detail is in the app. Do not write the missing sentence yourself.
- Tenant Strike does not establish that the findings in a path concern the same user or resource. Do not say they do.
- The scan does not measure whether an attack would succeed, whether data was reached, or whether logging would catch it. If the user asks, say those were not assessed.
- The connector withholds names, email addresses and resource identifiers. Do not guess or invent them.
- If `context.snapshot.datasetKind` is `synthetic`, say that this is sample data.
