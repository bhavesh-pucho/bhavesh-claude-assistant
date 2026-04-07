# Pucho AI Studio

**Summary**: Fork of Activepieces v0.79.3 with Workers 3.0 architecture and 425+ integrations.
**Tags**: #pucho-ai-studio #activepieces #automation #workflows
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Overview

- Internal name: "Pucho AI Studio"
- Fork of Activepieces v0.79.3
- Workers 3.0 architecture (upgraded from v0.70.1)
- 425+ integrations across 15 categories
- Workflow JSON: `schemaVersion: 7`

## Configuration

- Redis must use `noeviction` policy (not `volatile-lru`)
- Dev mode: `AP_DEV_PIECES` set in server-level `.env`
- Agent/AI piece: `packages/pieces/community/ai/`

```bash
# Verify Redis policy
redis-cli CONFIG GET maxmemory-policy
# Should return: noeviction
```

## Related Notes

- [[pucho-ai-platform]]
- [[ec2-setup]]
