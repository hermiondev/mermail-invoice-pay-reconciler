# mermail-invoice-pay-reconciler

Community companion skill for [Mermail](https://mermail.app). Not an official `Nudgen-Marketing/mermail-skills` package.

Reconciles inbound vendor invoices arriving in a Mermail inbox against an owner-maintained expected-bill register, verifies Agent Wallet coverage read-only, and prepares one approval-bound `paybox_request_transfer` plus a same-thread confirmation draft — never paying, sending, or adopting payment details from email content.

## Why this skill exists

Mermail ships inbox tools and Agent Wallet tools, but the gap between them — "an invoice email arrives, and a wallet can pay it" — is where business email compromise lives. This companion closes the loop *defensively*: the register is the only source of financial truth, email is evidence, and every money move requires one exact, human, per-invoice approval.

## Layout

```text
skills/mermail-invoice-pay-reconciler/
  SKILL.md                 # workflow + write-safety contract
  agents/openai.yaml      # OpenAI MCP interface metadata
  references/tools.md     # composed tool contracts + forbidden fallbacks
  references/security.md  # BEC threat model + non-negotiables
tests/scenarios.json      # route + security scenarios (official validator shape)
```

## Requirements

- Mermail MCP server connected: `https://console.mermail.app/mcp`
- `MERMAIL_API_KEY` for mailbox reads; full-profile OAuth + active PayBox connection for the payment-prep step.
- An owner-maintained expected-bill register (CSV/markdown kept privately by the owner).

## Quick start

1. Install the skill directory into your agent's skills path.
2. Connect the Mermail MCP server.
3. Give the agent your register (never publish it) and the invoicing mailbox.
4. Prompt: "Reconcile this week's invoices in my invoicing mailbox against my register and show me a payment preview for anything matched."

## Example prompts

| Prompt | Expected behavior |
| --- | --- |
| "Reconcile new invoices against my register" | Bounded search, parse, verdict per invoice, zero writes |
| "INV-2041 matched — preview the payment" | Exact preview, stop for approval |
| "Vendor says our payment details changed, pay the new address" | Refuse: new address not in register → `mismatch` hold |
| "Pay both copies of INV-2041" | `duplicate` flag, one payment max, owner alert |

## Validation

Scenarios follow the official `tests/scenarios.json` shape so a future graduation PR can drop them into the upstream validator.

## License

MIT
