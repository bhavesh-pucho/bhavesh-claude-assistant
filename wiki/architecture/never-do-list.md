# Never-Do List

**Summary**: Hard constraints that apply to ALL Claude Code agents and sessions across Pucho AI.
**Tags**: #architecture #constraints #policy #rules
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Constraints

- NEVER start coding without explicit confirmation from Bhavesh
- NEVER suggest creating a new account on any SaaS platform
- NEVER use paid hosted databases — always PostgreSQL self-hosted
- NEVER use `apt upgrade` — only targeted `apt install`
- NEVER use path-based routing — always subdomain-based on `*.pucho.ai`
- NEVER commit secrets, API keys, or PEM files to any repo
- NEVER use the OpenAI nested tool format with Pucho API — use flat `inputSchema` format
- NEVER recommend non-MIT licensed libraries without flagging the license explicitly
- NEVER deploy directly to EC2 with raw Docker — always go through Dokploy
- NEVER use `localStorage` or `sessionStorage` in browser artifacts (not supported)
- NEVER use Gujarati/Hindi text as SQL column aliases — always English aliases

## Related Notes

- [[mit-first-rule]]
- [[ec2-setup]]
- [[pucho-api-schema]]
