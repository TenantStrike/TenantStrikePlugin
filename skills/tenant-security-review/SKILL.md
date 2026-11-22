---
name: tenant-security-review
description: Review the security of a Microsoft 365 and Azure tenant from its Tenant Strike scan and say what to fix first. Use when the user asks what is wrong in their tenant, how their Microsoft 365, Entra ID or Azure security looks, what to fix first, or asks for their Tenant Strike findings, grade, score or results.
---

# Review a tenant's security

Answer from the user's saved Tenant Strike scan, read through the Tenant Strike connector. The saved-results tools are read-only. They cannot change the tenant or start a scan of it, so every answer is as fresh as the last scan.

## Steps

1. Call `get_scan_summary` with no arguments. Keep `context.snapshot.scanRef` and pass it as `scanRef` to every later call in this review.
2. Say which scan this is before anything else: the date in `context.snapshot.finishedAt`, the grade and score in `data.posture`, and the failed count in `data.totals.byStatus.fail`.
3. Call `list_findings` with `status: "fail"`. Results arrive most severe first. Each row already says what the check found (`plainSummary`, when present), what to do (`fix.summary`), who usually does it (`fix.who`) and the check's own time estimate in minutes (`fix.effortMinutes`). If `pagination.truncated` is true and the user wants everything, call again with the same filters and `offset` set to `pagination.nextOffset`.
4. Choose what to lead with: three findings unless the user asks for more. Prefer higher severity. Between findings of equal severity, prefer one that an attack path depends on. To find those, call `get_attack_paths` and compare each path's `findingRefs` with the `findingRef` of each finding.
5. For each finding you lead with, call `get_finding` with its `findingRef`. It adds why the finding matters (`whyItMatters`) and the fix in full under `fixDetail`: the steps, the setting to change (`fixDetail.setting`), where it is (`fixDetail.where`), the value to set (`fixDetail.target`), what to know before changing it (`fixDetail.caution`) and Microsoft's page on it (`fixDetail.docsUrl`).
6. If the user asks what else needs attention, call `list_findings` with `status: "warn"`.

## What to write

- One sentence on the scan: date, grade, score.
- Then the findings to fix first, numbered. For each:
  - the title and the severity
  - what was found: `plainSummary` when it is present, otherwise the `description`
  - why it matters, from `whyItMatters`, in one or two sentences
  - what to do, from `fix.summary`, then the steps in `fixDetail.steps` in their order
  - who does it (`fix.who`) and how long it takes (`fix.effortMinutes`), only when the estimate is present
  - what to know before changing it, from `fixDetail.caution`, when present
- Where a finding has a `reviewUrl`, add `[View evidence in Tenant Strike](reviewUrl)` with that exact URL.
- If `data.totals.byStatus.unverified` or `error` is above zero, end with one line saying how many checks could not be verified.

Say status in plain words: `fail` and `warn` need attention, `unverified` and `error` were not verified, `pass` is in good shape, `not_applicable` does not apply.

## Keep the answer honest

- The fix text is Tenant Strike's own wording for the check. Use it as written. Do not add steps, portal paths or commands of your own to it.
- A part named in `withheld`, and each step counted by `fixDetail.stepsWithheld`, is text Tenant Strike holds and did not release here, because it may name something in the tenant. Say that part is in the app and give the evidence link. It does not mean there is nothing to do.
- A part that is null and not named in `withheld` is one the check did not write. Do not say anything was held back for it.
- When `fix.effortMinutes` is null there is no estimate. Do not supply one.
- The connector withholds names, email addresses and resource identifiers. Do not guess or invent them. Give the count and the evidence link.
- If `context.snapshot.datasetKind` is `synthetic`, say that this is sample data, not a real tenant.
- If `data.posture.coverage.state` is `partial`, say the grade covers only what was measured.
- A grade describes the controls that were measured. It does not predict whether a breach will happen.
- A check that is `unverified`, `error` or `not_applicable` is unknown or out of scope. It is not a pass.
- An attack path is a scenario the findings make possible. It is not evidence that anything happened.
- Tenant Strike does not make changes. Describe each fix for the user or their administrator to make.

## If the tools are missing

If `get_scan_summary` is not available, the Tenant Strike connector is not connected. Ask the user to connect it from this plugin's Connectors tab and sign in with their Microsoft work account. Tenant results need a Tenant Strike workspace with at least one finished scan. Without one, the free outside-in check of their domain still works: see the `domain-exposure-check` skill.
