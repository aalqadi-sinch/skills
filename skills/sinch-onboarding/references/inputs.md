# Onboarding inputs

> Variable contract for the onboarding guides. Values come from the launcher prompt or the user's account, never from this file. Nothing here is schema authority; API facts come from the canonical documents the product skills link.

A launcher prompt starts onboarding and supplies known values, for example:

```text
Use the sinch-onboarding skill to onboard me on Sinch. Follow references/agents/claude-code.md in that skill.
Use the following data:
- name: Ata
- product: sms
- region: us
- project id: 00000000-0000-0000-0000-000000000000
- current balance: 2.00 USD
- trial ends: 2026-10-22
```

Match launcher labels to the variables below case-insensitively, treating spaces, hyphens, and underscores as the same. A label not in this table is still a known fact: keep it, and do not ask for it again.

## Variables

| Variable | Launcher label | Secret? | If missing |
|----------|----------------|---------|------------|
| `USER_NAME` | name | no | Greet without a name. Do not ask. |
| `PRODUCT` | product | no | Infer from the request; ask only if unclear. Covered: `sms`. |
| `REGION` | region | no | `us` when every recipient is a US (+1) number, else `global`. |
| `ACCOUNT_ID` | account id | no | Optional. Only for the user's reference; ask only if the user wants it shown. |
| `PROJECT_ID` | project id | no | Ask once. The user finds it in the dashboard. |
| `APP_ID` | app id, conversation app id | no | After credentials work, list the project's Conversation API apps (read-only). One app: use it. Several: ask which. None: guide the user to create one. |
| `APP_REGION` | app region | no | Read from the app record when listed; else default to the product guide's region. |
| `ACCOUNT_STATUS` | account status, plan | no | `trial` when trial dates or a trial number are given; else ask once. |
| `TRIAL_START` | trial start, account created | no | Optional when `TRIAL_END` is known. |
| `TRIAL_END` | trial end, trial ends | no | `TRIAL_START` plus `TRIAL_LENGTH_DAYS` if both are known; else ask once. |
| `TRIAL_LENGTH_DAYS` | trial length | no | Leave unset. Do not assume a length. |
| `DAYS_LEFT` | days left | no | Always recompute from `TRIAL_END` against today's date (UTC). A launcher value is a snapshot; prefer the recomputed one and say if the trial has ended. |
| `CREDIT_BALANCE` | current balance, credits | no | Ask once, when a step depends on it. The value is a snapshot from when the launcher was written; ask the user to re-check the dashboard before any step where the balance matters. Never compute a new balance from guessed message prices. |
| `CREDIT_TOTAL` | credit total, starting credits | no | Optional. |
| `SENDER_NUMBER` | sender, trial number | no | List the project's numbers (read-only). On a trial, use the trial number the dashboard shows; ask if there is more than one candidate. |
| `VERIFIED_DESTINATIONS` | verified numbers, verified destinations | no | Ask once. The user sees them in the dashboard. Treat an unlisted number as unverified. |
| `MAX_VERIFIED_DESTINATIONS` | max verified numbers | no | Leave unset; if the dashboard reports a limit, use it. Do not assume one. |
| `SERVICE_PLAN_ID` | service plan id | no | Needed only from the go-live phase. Ask once, with help to find it in the dashboard. |
| `RECOMMENDED_TOPUP` | recommended top-up, upgrade amount | no | Do not suggest an amount. Point the user to the dashboard's upgrade page. |
| `COSTS` | costs, prices | no | Leave unset. Before any paid step, state the cost only from the launcher, the dashboard (as the user reads it), or a canonical Sinch page fetched this session; otherwise say the cost is unconfirmed and ask the user to check before approving. |
| `SKILLS_BASE` | skills base | no | Lovable only: `https://raw.githubusercontent.com/sinch/skills/main/skills/`. |
| `DASHBOARD_URL` | dashboard | no | `https://dashboard.sinch.com` |
| `SINCH_KEY_ID`, `SINCH_KEY_SECRET` | — | **yes** | Never accept these in the launcher or chat. The agent adapter's access key checkpoint has the user store them. If a launcher contains a secret, do not repeat it; tell the user to rotate that key. |

## Resolution order

For every variable, in order:

1. The launcher prompt.
2. Earlier messages in this conversation, and the workspace (config files, existing `SINCH_*` variables). Never print secret values.
3. A read-only API lookup, when the table names one and credentials already work.
4. Ask the user, once, with every missing value that blocks the current phase in one question. Do not ask for values a later phase needs until that phase starts.

Never invent an ID, number, date, or price. When a value changes (a new sender number after go-live, a new balance after an upgrade), update it in one place and use the new value from then on.

## Keep facts in one place

Non-secret values (`PROJECT_ID`, `APP_ID`, `APP_REGION`, `SENDER_NUMBER`, `VERIFIED_DESTINATIONS`) are configuration, not secrets: keep them in one server-side config location the integration reads. Secrets go only in the secret store the agent adapter names.
