---
name: security-brief
description: Write a short security brief about a Microsoft 365 and Azure tenant for a reader who is not technical, such as a business owner, a board or an MSP's client, from the tenant's Tenant Strike scan. Use when the user asks for an executive summary, a board update, a client report, a status email or a one-page summary of their tenant's security.
---

# Write a security brief for a non-technical reader

Write from the user's saved Tenant Strike scan, read through the Tenant Strike connector. The reader decides on budget and priorities. They do not configure Microsoft 365.

## Steps

1. Call `get_scan_summary` with no arguments and keep `context.snapshot.scanRef`.
2. Call `list_findings` with that `scanRef` and `status: "fail"`. Take the three most severe. Each row says what to do in `fix.summary`, who usually does it in `fix.who`, and the check's own time estimate in `fix.effortMinutes` when it has one.
3. Call `get_finding` for each of the three. `whyItMatters` says what an attacker does with the weakness, and `fixDetail.caution` says what to know before changing it.
4. Call `get_attack_paths` with the `scanRef`. If there are paths, pick the one with the highest impact to explain why the findings matter together. Its `route` says the path in a few words and its `narrative` tells the story.
5. Call `get_changes_since_last_scan` with the `scanRef`. If there is an earlier scan, note what was fixed and what is new.
6. Call `get_accepted_risks` with the `scanRef`. If any risks have been accepted, give the count.

Ask who the reader is if the user has not said. An owner wants the decision. A board wants the trend. A client wants to know what their provider is doing about it.

## What to write

No more than 300 words, in this order, with these headings or the user's own:

- **Where we stand.** The grade and score, the date of the scan, and one sentence on what the grade means.
- **The three things that matter most.** Each in one or two sentences: what could go wrong for the business, not the name of the setting. Put the technical name in parentheses once.
- **What we are doing about it.** The fix for each, who makes it, and how long it takes where the scan gives an estimate.
- **What changed.** One or two sentences, only if there is an earlier scan.
- **What we could not check.** One sentence if any checks were not verified.

## How to write it

- Write for someone who has never opened an admin portal. Say "anyone in the company can approve a new app's access to mail" rather than naming the policy.
- Use numbers the scan gives you. Do not add statistics, costs, probabilities or time estimates of your own.
- Do not name people, mailboxes or devices. The connector withholds them, and a brief that travels should not carry them.
- Do not say the organization is secure, safe or compliant. Say what was measured and what was found.
- An attack path is a scenario, not an incident. Do not write it as something that happened.
- If `datasetKind` is `synthetic`, say at the top that this brief is based on sample data.
